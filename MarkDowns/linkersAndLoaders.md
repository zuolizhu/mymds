---
title: "LinkersAndLoaders"
date: 2017-12-12T18:22:29-05:00
draft: true
---

My notes for lnikers and loaders.

A loooooot of concepts...

---

Some basic concepts:

- **Linking**: Combining two or more object programs into a single unit
- **Loading**: Load an object program **into memory** for execution.
- **Relocation**: Load program from a diffenent location



---

#### Loader

- Basic functions
  - Allocate space in memory for a program
  - Place the program into memory
  - start executing program
- Additional functions
  - linking and relocation
  - use without program translators (assemblers, compilers)
    - Object code is often in the same format



- Type of Loader

  - Assemble and go

    - Places translated instructions and data directly into their assigned memory
      locations as they are assembled
    - When assembly is completed, the assembler causes a transfer to the start
      address of the program
    - Program has to be assembled every time it needs to be executed

  - **Absolute (Bootstrap loader)**

    - Does **not** perform relocation

    - Needed for programs associated with the operating system

    - Programmer needs to explicitly specify actual addresses

    - **Very simple to design and use**

      - The object program is loaded at the address specified on the START
        directive
      - No linking or relocation is required

    - **Single Pass operation**

      - Check header record to verify correct program has been fetched for
        loading
      - Read each text record and move object code into the specified
        address in memory
      - When the End record is reached, jump to the specified address to begin
        execution of the loaded program

    - **Disadvantages of Absolute Loaders**

      - Actual addresses must be specified
      - Be careful not to assign two subroutines to the same or overlapping locations
      - Difficult to use libraries (e.g. string, math)
        - Need to select and load exactly those routines that are needed
        - Can’t be done effectively if all subroutines have pre-assigned absolute addresses

    - #### Bootstrap Loader

      - Special type of absolute loader
      - Automatically executed when the computer is first turned on
      - Loads the first program to be run (Usually the OS)


  - Relative or Relocating
    - Carries out relocation
    - Needed for users' programs



---

#### Program Linking

- A program may be split into multiple **control sections**

  - These sections can be:
    - Assembled independently
    - Loaded at non consecutive addresses in memory
  - External references to symbols in other segments may only be resolved after these segments are loaded in memory
  - Assembler directives EXTDEF and EXTREF
  - The Assembler produces an External Definition Table (EDT) and an External Reference Table (ERT) for each module.
  - A **Simplified View** of the Linking Process
    - The EDT for a module maintains a list of the external symbols that are provided by the module
    - The ERT for a module maintains a list of the external symbols that are needed by the module
    - The Linker must ensure that each external symbol needed by a module is provided by another

- Linking Steps

  - Obtain load point for each module
  - Combine the separate EDTs into a single EDT
  - Obtain the External Symbol Table (EST) from the Combined EDT by adding to each relative address the load point of the corresponding module.
  - Use the External Symbol Table to resolve each external reference

- #### Control sections

  -  The assembler handles external references 
    - Uses EXTDEF and EXTREF directives and "placeholders"

---

#### Program Relocation

- Relocation is required when running serverral independent programs at the same time
  - Need to share memory and CPU
- Two methods for relocation
  - Modification record
    - Describe each part of the object code that must be changed when program is relocated
    - Suitable for small number of changes
    - Use of PC and Base relative addresses can avoid the need for many address modifications
  - Relocation bit
    - Suitable for large number of changes
    - If too many modification records are need, it would be more efficient to use a bit mask instead
      - Each instruction is associated with a bit
      - Set bit to 1 if relocation is needed and to 0 otherwise
      - If the bit corresponding to a word is set, the program’s starting address will be added to the word when the program is relocated
      - Note: For SIC, there is 1 bit for each instruction (all SIC instructions occupy one word)
- Assembler sets start address at zero
- Fills in relative addresses while assembling instructions
- Produces a Modifier record for each instruction which needs relocation
- Each modifier record contains starting byte address and the length of the address field to be modified
- Modifier records follow the text records in the object program

---

#### Modifier Records

