File Content Indexing System
This is a distributed system implemented in C# using .NET 6.0, designed to index words in .txt files across two directories using two Scanner processes (ScannerA and ScannerB) and aggregate the results in a Master process. ScannerA and ScannerB read .txt files, count word occurrences, and send data to the Master via named pipes. The Master receives, aggregates, and displays the word counts in the format filename:word:count. The system utilizes multithreading and CPU core affinity to ensure efficient processing and meets the requirements of a distributed indexing application.
Project Structure

ScannerA/
ScannerA.cs: Source code for ScannerA, which processes .txt files in a specified directory, indexes words, and sends data via a named pipe (e.g., agent1) on CPU core 1.
ScannerA.csproj: Project file for building ScannerA.


ScannerB/
ScannerB.cs: Source code for ScannerB, similar to ScannerA but processes a different directory and uses a different pipe (e.g., agent2) on CPU core 2 (or core 1 if fewer cores are available).
ScannerB.csproj: Project file for building ScannerB.


Master/
Master.cs: Source code for Master, which listens on two named pipes, aggregates word counts from ScannerA and ScannerB, and displays results on CPU core 0.
Master.csproj: Project file for building Master.


FileContentIndexingSolution.sln: Solution file to manage all three projects.
bin/Debug/: Contains compiled executables (ScannerA.exe, ScannerB.exe, Master.exe) after building.

Commit History
The project was developed in stages, with each stage committed separately to track progress:

Initial Setup (2025-06-01): Created solution with three console projects (ScannerA, ScannerB, Master) and initialized project files.
Scanner Implementation (2025-06-02): Implemented file reading and word indexing logic for ScannerA and ScannerB, incorporating multithreading for reading files and sending data.
Master Implementation (2025-06-03): Added named pipe server and data aggregation logic for Master, with threads for handling pipes and displaying results.
CPU Affinity (2025-06-04): Configured processor affinity to assign Master to core 0, ScannerA to core 1, and ScannerB to core 2 (with fallback to core 1 for limited-core systems).
Error Handling and Testing (2025-06-05): Enhanced error handling for directory access and pipe connections, added detailed logging, and resolved issue where Scanner consoles closed prematurely by implementing Console.ReadLine() and retry logic for pipe connections.

Dependencies

.NET 6.0 or later: Required for building and running the console applications.
Windows OS: Necessary for ProcessorAffinity functionality to assign processes to specific CPU cores.

Notes

Multithreading:
ScannerA and ScannerB: Each uses one thread for reading .txt files and one for sending data via named pipes.
Master: Uses one thread per named pipe (two pipes) and one thread for displaying aggregated results.


CPU Affinity:
Master: Assigned to CPU core 0.
ScannerA: Assigned to CPU core 1.
ScannerB: Assigned to CPU core 2, with fallback to core 1 if fewer than three cores are available.


Communication: Uses NamedPipeServerStream (Master) and NamedPipeClientStream (Scanners) for inter-process communication.
Output: Master displays word counts in the format filename:word:count, updated every 2 seconds.
Error Handling: Scanners include retry logic for pipe connections (up to 5 attempts) and checks for directory existence, with console output to aid debugging.

