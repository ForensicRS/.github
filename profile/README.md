# ForensicRS

**forensic-rs** is a framework for forensic tools written in Rust, designed to facilitate the analysis of artifacts by providing reusable libraries that can be shared across different projects. Its modular architecture enables easy integration of new parsers and forensic data writers, promoting code reuse and consistency between tools.

The core idea behind the framework is to enable the reuse of forensic artifact analysis tools across various contexts. To achieve this, forensic-rs decouples the code that parses or reads artifacts from the code that accesses the extracted values—such as registry keys, files, or SQL rows. This separation allows the same analysis logic to be applied whether the artifacts are located directly on the file system or encapsulated within compressed archives (e.g., ZIP files) resulting from triage processes.

This design makes the framework highly versatile, supporting use cases such as triage processors like Plaso, modules within EDR systems, or even graphical tools like Eric Zimmerman's Registry Explorer. Moreover, forensic-rs benefits from Rust's performance, reliability, and safety while offering easy integration into Python scripts through bindings or FFI.

## Features

- High-performance forensic artifact parsing
- Modular architecture
- Reusable libraries for multiple forensic projects
- Decoupled parsing and data access layers
- Multiple output writers (JSONL, SQLite, Elasticsearch)
- Native Rust implementation with no SO dependant libraries. 
- Easy integration with Rust and Python tools

## Available modules

### Main library

Provides the building blocks, structures and traits: https://github.com/ForensicRS/forensic-rs

### Readers

Readers allow access to data contained in different types of artifacts, such as hive files, sqlite databases, esedb files...

#### Registry

Implements [*RegistryReader*](https://github.com/ForensicRS/forensic-rs/blob/main/src/traits/registry.rs#L200) using the Windows API to access the registry of a live system: https://github.com/ForensicRS/frnsc-liveregistry-rs


#### Hive

Implements [*RegistryReader*](https://github.com/ForensicRS/forensic-rs/blob/main/src/traits/registry.rs#L200) to read hive files: https://github.com/ForensicRS/frnsc-hive

#### Sqlite

https://github.com/ForensicRS/frnsc-sqlite

#### EseDB (WiP)

The ESEDB reader is a fully native Rust implementation designed to access data stored in Extensible Storage Engine (ESE) databases, a proprietary format used by various Windows artifacts such as SRUM, UAL, and Windows Search. Unlike most existing solutions, which rely on platform-specific libraries or bindings, this reader is being developed from scratch in Rust to offer cross-platform compatibility without external dependencies. Although still under development, it aims to become one of the few alternatives capable of accessing ESEDB data seamlessly on any operating system, enabling forensic analysis workflows across Windows, Linux, and macOS environments.

https://github.com/ForensicRS/frnsc-esedb

### Parsers and Analyzers

Parsers and analyzers interpret the information extracted by readers, transforming raw data into meaningful insights. Although both SRUM and UAL artifacts use ESE files as their storage format, the information they contain serves distinct purposes. Contextualizing the extracted data is essential to enhance the quality of the analysis, as the same data structure may represent different concepts depending on the artifact. This separation allows forensic tools to apply artifact-specific knowledge, improving the accuracy and reliability of the final analysis.

#### Prefetch

The Prefetch analyzer extracts information from Windows Prefetch files, which are binary artifacts used by the operating system to optimize the startup of frequently executed applications. Each Prefetch file contains execution metadata such as the executable's path, the last execution timestamp, and a count of how many times the program has been run. Additionally, it lists the files accessed by the application during its execution. This information is highly valuable in forensic investigations, as it helps reconstruct program execution timelines and detect suspicious activity. The analyzer parses the Prefetch structure, exposing all its fields in a structured format that can be easily processed by other components of the framework.

https://github.com/ForensicRS/frnsc-prefetch

### AmCache

The Amcache analyzer processes data from the Amcache artifact, which stores information about executed programs and installed applications on Windows systems. Located in the Amcache.hve registry hive, this artifact contains detailed records such as file hashes, file paths, installation dates, and digital signatures. The Amcache analyzer extracts these entries and correlates them with executable files, providing insight into what software was executed on the system, when it was first seen, and whether it was signed or potentially suspicious. This artifact plays a key role in forensic investigations by offering a persistent record of program execution, even after the original files have been deleted or the Prefetch files have been cleaned.

https://github.com/ForensicRS/frnsc-amcache


### Writers

Writers are responsible for storing the processed data obtained from artifact parsers in various output formats. They act as the final stage of the processing pipeline, transforming the extracted and analyzed information into a structured representation that can be easily stored, queried, or shared. Writers abstract the complexity of different storage backends, allowing the same forensic analysis tools to export their results to multiple destinations such as JSONL files, SQLite databases, or Elasticsearch instances without requiring changes to the analysis logic. This design ensures flexibility and consistency when integrating forensic tools into different workflows or environments.

https://github.com/ForensicRS/frnsc-writers


### Other Tools

#### Triage

The low-level file copy library provides a reliable and efficient mechanism to extract forensic artifacts directly from the file system, even in scenarios where standard file access methods might fail due to permissions or file locks. This library enables the selective copying of files while preserving their original metadata, such as timestamps and access permissions, ensuring the integrity of the collected evidence.

Additionally, the library supports the creation of triage ZIP archives, allowing the packaging of multiple artifacts into a compressed container. This functionality facilitates the remote collection of key forensic data from a system, optimizing both storage and transfer while maintaining the forensic integrity of the original files. The library is designed to operate at a low level to minimize its footprint on the system, making it suitable for both live acquisition and offline analysis workflows.

https://github.com/ForensicRS/frnsc-triage

### Full module list

* [ForensicRS](https://github.com/ForensicRS/forensic-rs)
* [Registry](https://github.com/ForensicRS/frnsc-liveregistry-rs)
* [Hive](https://github.com/ForensicRS/frnsc-hive)
* [SqLite](https://github.com/ForensicRS/frnsc-sqlite)
* [EseDB](https://github.com/ForensicRS/frnsc-esedb)
* [Prefetch](https://github.com/ForensicRS/frnsc-prefetch)
* [AmCache](https://github.com/ForensicRS/frnsc-amcache)
* [Writers](https://github.com/ForensicRS/frnsc-writers)
* [TaskBar](https://github.com/ForensicRS/frnsc-taskbar)
* [Triage](https://github.com/ForensicRS/frnsc-triage)