### MC6800 Pinball ROM Reverse Engineering
## Introduction
This repository contains several Ghidra projects that analyze some pinball ROMs.
## Contents
  - `README.md`
    This file
  - `Using_Ghidra_and_Claude.md`
    Document (written with the help of Claude) about using Ghidra with Claude
  - ROM disassemblies, packaged as Ghidra Archive files (to open with Ghidra, see below)
      - `01a-leds_*.gar`: Pincoder Test ROM
      - `black_knight_*.gar`: Black Knight (1980, Williams)
      - `eightball_*.gar`: Eight Ball (1977, Bally)
      - `f14_*.gar`: F-14 Tomcat (1987, Williams)
      - `will7ver3_*.gar`: Leon Borre's test ROM
## How to Use a .gar File
To open and use a project archive, you must restore it to your local filesystem:
  - Open Ghidra: Launch the Ghidra Project Window.
  - Restore the Project:
    - Go to File > Restore Project....
    - In the dialog box, select the .gar file you want to open.
  - Choose a Destination:
    - Specify a Project Directory where Ghidra should extract the files.
    - Give the project a name.
  - Complete the Extraction:
    Click OK. Ghidra will decompress the archive and recreate the project structure in the folder you selected. Once finished, the project will open automatically in your Project Manager. 