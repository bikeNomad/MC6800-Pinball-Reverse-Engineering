# Unlocking Vintage Pinball Secrets: Using Ghidra and Claude to Decode MC6800 ROM Code

If you've ever wondered what's really happening inside your vintage Bally, Stern or Williams pinball machine, you're in for a treat. Modern AI tools have made it surprisingly accessible to peek under the hood of these classic games and understand the code that brings them to life. In this guide, I'll show you how to use Ghidra (a professional reverse engineering tool) combined with Claude AI to analyze the MC6800 processor code found in 1970s and early 1980s pinball machines.

## **What We're Working With**

Vintage pinball machines like Bally's "Eight Ball" (1977) use a Motorola MC6800 microprocessor—one of the earliest 8-bit CPUs used in pinball. The game's logic, sounds, scoring, and display handling are all contained in ROM chips, typically just a few kilobytes in size. While the code is compact, it's written in assembly language, which can be cryptic to read without help.

That's where our tools come in:

  - **Ghidra** - A free, open-source reverse engineering suite developed by the NSA (the US National Security Agency)
  - **GhidraMC6800** - A plugin that adds proper MC6800 processor support to Ghidra
  - **GhidrassistMCP** - A bridge that connects Ghidra to Claude AI via the Model Context Protocol (MCP)
  - **Claude Code** - An AI assistant that can help explain and analyze the assembly code

Together, these tools let you load a ROM file, see the disassembled code, and have a natural conversation with Claude about what the code is doing—all without needing a computer science degree.

## **Setting Up Your Analysis Environment**

### **Step 1: Install Ghidra 12.0**

  1. **Download Ghidra 12.0** from the official GitHub releases page: [https://github.com/NationalSecurityAgency/ghidra/releases](https://github.com/NationalSecurityAgency/ghidra/releases)
  2. Look for the release tagged `Ghidra_12.0` and download the appropriate package for your operating system
  3. **Extract the archive** to a location on your computer (e.g., `C:\Tools\ghidra_12.0` on Windows or `~/Tools/ghidra_12.0` on Mac/Linux)
  4. **Install Java Development Kit (JDK) 21** if you don't already have it—Ghidra 12.0 requires this version
  5. Run Ghidra by executing `ghidraRun.bat` (Windows) or `ghidraRun` (Mac/Linux) from the extracted folder

### **Step 2: Install the GhidraMC6800 Plugin**

The standard Ghidra installation doesn't fully support the MC6800 processor used in vintage pinball machines, so we need to add specialized support.

  1. **Download GhidraMC6800** from: [https://github.com/sigurasg/GhidraMC6800/releases](https://github.com/sigurasg/GhidraMC6800/releases)
  2. Find the release that matches Ghidra 12.0 (look for tags indicating compatibility)
  3. Download the `.zip` file containing the plugin
  4. **In Ghidra**, go to `File → Install Extensions`
  5. Click the green "+" icon in the top-right corner
  6. Navigate to and select the downloaded GhidraMC6800 `.zip` file
  7. Restart Ghidra when prompted

This plugin adds proper instruction decoding, calling conventions, and other MC6800-specific features that make analyzing pinball ROMs much more accurate.

### **Step 3: Install the GhidrassistMCP Plugin**

This plugin is the bridge between Ghidra and Claude AI, allowing Claude Code to access your Ghidra project through the Model Context Protocol.

  1. **Download GhidrassistMCP** from: [https://github.com/jtang613/GhidrassistMCP/releases](https://github.com/jtang613/GhidrassistMCP/releases)
  2. Find the release compatible with Ghidra 12.0
  3. Download the plugin `.zip` file
  4. **In Ghidra**, go to `File → Install Extensions` again
  5. Click the green "+" icon and select the GhidrassistMCP `.zip` file
  6. Restart Ghidra

### **Step 4: Set Up Claude Code with MCP Support**

Claude Code is a command-line tool that lets Claude interact directly with your system, including Ghidra through the MCP (Model Context Protocol) connection.

  1. **Install Claude Code** by following the instructions at: https://docs.claude.com/docs/claude-code
  2. **Configure the MCP server** for GhidrassistMCP:

      - Locate your Claude Code configuration file (typically `~/.claude/config.json` on Mac/Linux or `%USERPROFILE%\.claude\config.json` on Windows)
      - Add the GhidrassistMCP server configuration. In the JSON you will want to add a `ghidrassist` section to the `mcpServers` entries. A `config.json` file with just the GhidrassistMCP plugin looks like this (change the default port `8080` to match what’s set up in the plugin configuration window if you’ve changed it there):

```json
{
  "mcpServers": {
    "ghidrassist": {
      "url": "http://localhost:8080/sse"
    }
  }
}
```

    3. **Start**: Launch Claude Code from your terminal
    4. Verify the MCP connection is working by checking that Claude Code can see the Ghidrassist tools

### **Step 5: Connect Everything Together**

- **Start Ghidra** and create a new project (or open an existing one)
- **Enable the GhidrAssistMCP plugin** by going to `File → Configure → Miscellaneous` and checking the box next to GhidrAssistMCPPlugin
- **Start the MCP server** from within Ghidra (`Window/GhidrAssistMCP`), choose `localhost` and a port (`8080` is the default; change this if you already have something running on that port)
- **Launch Claude Code** in your terminal
- You should now be able to interact with your Ghidra project directly through conversation with Claude

## **What Claude Can Do With Your Ghidra Project**

Through the GhidrassistMCP connection, Claude Code gains powerful capabilities for analyzing your ROM code. Here's what Claude can help you with:

### **Reading and Understanding Code**

**Get the current selection**: Claude can retrieve whatever code you've highlighted in Ghidra's Listing window. Just select some assembly instructions and ask Claude "What does this code do?" or "Can you explain the selected code?"

**Retrieve specific functions**: If you know the address of a function you want to understand, you can ask Claude to "explain the function at address 0x1A40" and Claude will retrieve and analyze it for you.

**Read decompiled code**: For a higher-level view, Claude can access Ghidra's decompiler output, showing you a C-like representation of the assembly code (though this works better for some processors than others).

**Get function signatures**: Claude can retrieve detailed function signatures that Ghidra has identified, including parameter types, return values, and calling conventions.

### **Navigating the Program**

**Find functions**: Claude can search through your Ghidra project to find functions by name or pattern, helping you locate specific routines like "find functions related to scoring" or "show me the switch handling code."

**List all functions**: Claude can give you a complete inventory of all functions Ghidra has identified in your project, making it easier to get an overview of the program structure.

**Explore data references**: Claude can identify what data a particular piece of code accesses, helping you understand which RAM locations, ROM tables, or I/O ports the code interacts with.

**Get cross-references**: Claude can show you all the places in the code that reference a particular address—useful for understanding how data structures or functions are used throughout the program.

**Get memory maps**: Claude can show you the overall structure of your ROM—where code sections are, where data tables live, and how the memory is organized.

### **Analysis and Documentation**

**Explain assembly instructions**: Claude can break down complex assembly sequences step-by-step, explaining what each instruction does and how they work together.

**Identify patterns**: Claude can recognize common programming patterns like switch matrix scanning, BCD arithmetic, table lookups, and state machines.

**Add context**: Based on what it sees in the code and typical pinball machine architecture, Claude can suggest what hardware registers or game features specific code sections might be controlling.

**Answer specific questions**: You can ask targeted questions like "What value is being stored at 0x00B6?" or "Why is this code looping 8 times?" and Claude will analyze the relevant code to answer.

### **Interactive Documentation and Organization**

This is where the new GhidrassistMCP capabilities really shine—Claude can now help you actively document and organize your analysis:

**Rename functions and labels**: As you figure out what code does, ask Claude to rename it: "Rename this function to 'scan_switch_matrix'" or "Label this as 'player_score_bcd'". Claude will update the names directly in your Ghidra project.

**Add comments**: Instead of manually adding comments, you can ask Claude to document your findings: "Add a comment explaining this is the tilt handling routine" or "Comment that this loops through all 8 columns". Claude will add the comments to your Ghidra project.

**Set function signatures**: Once you understand a function's parameters and purpose, you can ask Claude to formalize it: "Set this function signature to take two byte parameters" or "Mark this as returning a boolean value".

This collaborative approach means you can focus on understanding the code while Claude handles the mechanical work of documenting your discoveries in Ghidra.

### **The Power of Conversational Analysis**

The real magic is that you don't need to learn a complex tool interface or memorize commands. You simply have a conversation with Claude while you explore the code in Ghidra. Claude can see what you're looking at, understand the context of your questions, help you build up your understanding piece by piece, and actively help you document what you learn.

## **Loading and Analyzing a ROM Set**

Now comes the fun part—actually digging into the code from a vintage pinball machine. Let's walk through analyzing a typical ROM set using Bally's "Eight Ball" as an example.

### **Importing the ROM Files**

1. **In Ghidra**, create a new project: `File → New Project → Non-Shared Project`
2. Give it a meaningful name like "Eight_Ball_Analysis"
3. **Import the ROM file(s)**: `File → Import File`
4. Navigate to your ROM dump files (these are usually `.bin` or `.rom` files obtained from the pinball community)
5. **Important**: When prompted for the processor:

    - Select **MC6800** (this option should now appear thanks to the GhidraMC6800 plugin)
    - Set the language to "MC6800:BE:16:default" (Big Endian, 16-bit address space)
    - Set the **base address** correctly—for Bally games, ROMs typically start at specific addresses like `0x1000` or `0xF000` (check the pinball's technical documentation)
    - If you have multiple ROM files (common in pinball machines), import each one at its correct memory address

### **Configuring the Memory Map**

Before you start analyzing your ROM, it's crucial to set up Ghidra's memory map to accurately reflect how the pinball machine's hardware organizes memory. This helps Ghidra understand where different types of memory exist and improves the quality of its analysis.

**Understanding Pinball Memory Organization**

Vintage pinball machines typically have several distinct memory regions:

- **RAM** - Working memory for game state, scores, switch states, etc. (often 0x0000-0x00FF or larger)
- **I/O or CMOS** - Memory-mapped I/O ports for hardware control (PIA chips, etc.)
- **ROM** - The game program code stored in ROM chips
- **Vectors** - Interrupt and reset vectors at the top of the address space

**Setting Up the Memory Map for Eight Ball**

Here's how to configure the memory map for a typical Bally game like Eight Ball:

1. **Open the Memory Map window**: In Ghidra's CodeBrowser, go to `Window → Memory Map`
2. **Add RAM regions**: Click the "+" button to add new memory blocks

    - **Name**: RAM
    - **Start Address**: 0x0000
    - **End Address**: 0x007f (or check your game's documentation)
    - **Length**: 0x80 (128 bytes is typical for early Bally games)
    - **Permissions**: Check R (Read), W (Write), and X (Execute)
    - **Block Type**: Default

3. **Add I/O regions**: Add blocks for memory-mapped I/O
    - **Name**: U10 (for the switch scanning PIA)
    - **Start Address**: 0x0088
    - **End Address**: 0x008b
    - **Length**: 0x4

4. Add additional blocks for U11, CMOS, or other I/O as documented for your game
    - Common ranges: 0x0090-0x0093, 0x0200-0x02ff

5. Define the ROM region
    - This is where your actual ROM data lives
    - **Start Address**: 0x1000
    - **End Address**: 0x1fff
    - **Length**: 0x1000 (4KB)


6. **Add the vector table region**: Because of incomplete address decoding in Bally hardware, the ROM also appears at higher addresses. However, to avoid seeing duplicate disassembled code throughout Ghidra, we only map the essential vector table region:
    - **Name**: VECTORS
    - **Start Address**: 0xfff8
    - **End Address**: 0xffff
    - **Length**: 0x8 (just 8 bytes for the vectors)
    - **Block Type**: Select **Byte Mapped**
    - **Source Address**: Point this to 0x1ff8 (the corresponding location in your loaded ROM)
    - This byte-mapped region gives Ghidra access to the critical MC6800 reset and interrupt vectors without duplicating the entire 4KB ROM image.

**Why This Approach Works**

In Bally pinball machines, the address decoding logic doesn't check all address lines, so the same ROM appears at multiple locations in the memory map. The most important alias is the vector table at 0xFFF8-0xFFFF because:

    - The MC6800 reset vector is at 0xFFFE-0xFFFF (tells the CPU where to start executing on power-up)
    - The interrupt vectors (IRQ at 0xFFF8, SWI at 0xFFFA, NMI at 0xFFFC) are also here

By only mapping these 8 bytes as an alias to the corresponding bytes in your ROM (at 0x1FF8-0x1FFF), Ghidra can find the vectors and understand where program execution begins, without cluttering your analysis with thousands of duplicate disassembly listings.

**Verifying Your Setup**

Your memory map should look similar to this example, with:

    - RAM at low addresses (0x0000-0x007F)
    - I/O regions where the hardware chips are mapped (U10 at 0x0088-0x008B, U11, CMOS)
    - ROM at 0x1000-0x1FFF (with your actual ROM file loaded here)
    - VECTORS at 0xFFF8-0xFFFF (byte-mapped to 0x1FF8 in the ROM)

The checkmarks in the R (Read), W (Write), and X (Execute) columns should reflect what each region is used for. ROM regions should be R and X but not W, while RAM should have all three.

### **Initial Analysis**

Once your ROM is loaded and memory map is configured:

1. **Double-click** the imported program in the project window to open it in the CodeBrowser
2. Ghidra will ask if you want to analyze the file—**click "Yes"**
3. In the analysis options dialog:

    - Keep most defaults checked
    - Ensure "MC6800 JSR Analyzer" is enabled (if available)
    - Click "Analyze"
    - Wait for the initial analysis to complete—this may take a few seconds to a minute

You'll now see the disassembled code in the main window. It will look like a lot of cryptic assembly instructions, but don't worry—Claude is about to help make sense of it.

### **Understanding What You're Looking At**

The Ghidra interface has several key areas:

- **Listing Window** (center): Shows the disassembled assembly code
- **Decompiler Window** (right): Shows an attempted C-like decompilation (useful but not always perfect for MC6800 code)
- **Program Trees** (left): Shows the memory layout and segments
- **Symbol Tree** (left): Lists functions and labels

### **Using Claude to Analyze Code**

Here's where the integration really shines. The workflow is simple and conversational:

1. **In Ghidra**, navigate to the code you're interested in—maybe you've found something that looks like it handles scoring or lamp control
2. **Select the code** by clicking and dragging in the Listing window to highlight the instructions you want to understand
3. **In your Claude Code terminal**, simply ask Claude about it

You don't use any special commands or menu items—just have a natural conversation. For example:

- "What does the selected code do?"
- "This looks like it's checking switches—can you explain the logic?"
- "Can you break down this function step by step?"
- "What hardware registers is this code accessing?"

Claude will automatically retrieve the selected code from Ghidra and provide an explanation in plain English.

**Example conversation:**

You select a block of code in Ghidra and type in Claude Code:

"I've selected some code at 0x1A40. Can you explain what this function does?"

Claude responds with a detailed analysis:

"This is a data table lookup and processing routine. Here's what it's doing:

1. It loads a pointer from RAM location 0x5A
2. It reads bytes from that structure and uses one as an index into a lookup table at 0x1A6F
3. It performs multiple mask operations to test conditions
4. It sets a result flag at RAM location 0xB6 based on the tests
5. It advances the pointer by 2 bytes for the next iteration

This pattern is typical of game state evaluation—checking if certain conditions are met for scoring, awards, or mode changes. The table at 0x1A6F contains the decoding information for whatever conditions this is testing."

You can then follow up with more questions:

"What might those conditions be testing in a pinball context?"

And Claude can provide more context based on typical pinball machine architecture and what it sees in the code.

**As you learn, Claude can help you document:**

"Can you rename this function to 'check_game_conditions' and add a comment explaining it checks scoring conditions?"

Claude will update your Ghidra project with the new name and comment, keeping your analysis organized as you go.

### **Typical Workflow for ROM Analysis**

Here's a practical approach to systematically understanding a pinball ROM:

### **Understanding How the Code Starts: Reset and Interrupt Vectors**

One of the most important steps in understanding a pinball ROM is figuring out where execution begins and how the system responds to events. The MC6800 processor uses **vectors** - special memory locations that contain addresses pointing to key routines.

**Finding the Vectors**

The MC6800 defines several important vectors at the top of the address space:

- **0xFFF8-0xFFF9**: IRQ (Interrupt Request) - Called by hardware timer interrupts
- **0xFFFA-0xFFFB**: SWI (Software Interrupt) - Called by SWI instruction
- **0xFFFC-0xFFFD**: NMI (Non-Maskable Interrupt) - Called by critical hardware events
- **0xFFFE-0xFFFF**: RESET - Where the CPU starts executing when powered on

Each vector is a 16-bit address stored in big-endian format (high byte first, then low byte).

**Examining the Vectors in Your ROM**

Let's look at how to find these in your Eight Ball ROM. You can ask Claude:

"What's in the IRQ vector at 0xFFF8?"

Claude will retrieve the bytes and tell you:

"The IRQ vector at 0xFFF8-0xFFF9 contains the bytes 0x19 0x62, which is the address 0x1962  in big-endian format. This means when a hardware interrupt occurs, the processor will jump to address 0x1962."

You can then navigate to that address and ask Claude to analyze the logic there.

**How Interrupts Work**

Understanding the IRQ vector is crucial because it shows you how the game's real-time operations work. In Eight Ball:

1. Hardware generates periodic signals connected to the CA1 lines on the U10 and U11 PIAs. These PIAs are configured to interrupt the MC6800 when these signals occur. The U10 interrupt is at 120Hz (AC line zero crossing) and the U11 interrupt is from a 555 timer and is used for display updates.
2. When an interrupt occurs, the MC6800:
    - Finishes executing the current instruction
    - Pushes the return address and processor status onto the stack
    - Reads the IRQ vector at 0xFFF8-0xFFF9
    - Jumps to address 0x1962 (the interrupt handler function)
    - The interrupt handler:
    - Examines the PIA control registers to figure out which interrupt happened
    - Jumps to either the display update or the switch scan handler
    - Returns via RTI (Return from Interrupt)
    - Normal program execution resumes where it was interrupted

This interrupt-driven architecture is why the switches feel responsive - they're being scanned dozens of times per second, regardless of what else the game code is doing.

**Finding the Reset Vector**

Similarly, you can find where the program starts:

"What's in the RESET vector at 0xFFFE?"

Claude will show you the address where execution begins when the game is powered on or reset. This typically points to initialization code that sets up the hardware, clears RAM, and starts the main game loop.

**Tracing Program Flow**

Once you know these entry points, you can start tracing through the code:

1. Start at the RESET vector to understand initialization
2. Follow the IRQ vector to understand real-time operations
3. Look for the main game loop (usually called after initialization completes)
4. Identify major subsystems by seeing what the IRQ handler and main loop call

You can ask Claude to help:

"Can you show me what the initialization code does after reset?"

"What functions does the IRQ handler call?"

This gives you a roadmap for understanding the entire program structure.

**2. Identify Key Routines**

    - Initialization code (runs at startup)
    - Main game loop (repeats continuously during play)
    - Interrupt handlers (respond to timed events)
    - Switch matrix scanning (reads button and target inputs)
    - Lamp/display drivers (controls lights and score displays)
    - Sound routines (triggers chimes or electronic sounds)

Ask Claude to help you find these: "Can you help me locate the main game loop?" or "Where's the interrupt handler?"

**3. Build Understanding Section by Section**

As you explore each area:

    - Select interesting code blocks
    - Ask Claude what they do
    - Request specific details: "Why is it looping 8 times here?"
    - Have Claude document your findings: "Add a comment that this scans 8 switch columns"
    - Rename functions as you identify them: "Rename this to 'init_hardware'"
    - Build up your understanding piece by piece

**4. Keep Your Analysis Organized** The beauty of having Claude help with documentation is that you can stay organized without breaking your analytical flow:
    - As soon as you understand what a function does, ask Claude to rename it
    - Add comments explaining tricky logic while it's fresh in your mind
    - Ask Claude to help you maintain consistent naming conventions: "What would be a good name for this function given that we've been using 'scan_' prefix for scanning routines?"

### **Finding Interesting Features**

Some fun things to look for in pinball ROMs, and questions you might ask Claude:

    - **Tilt handling** - "Can you find the tilt detection code?"
    - **Multiball logic** - "How does this game track multiple balls in play?"
    - **Special scoring modes** - "What's this code doing with the score—is it multiplying?"
    - **Attract mode sequences** - "Can you explain the attract mode light patterns?"
    - **Audit tracking** - "Where does the game store statistics?"
    - **Hidden adjustments** - "Can you find the operator adjustment menu code?"

Once you identify these features, you can ask Claude to help you organize your findings:

"Can you add labels to mark all the tilt-related functions we've found?"

## **Tips for Successful Analysis**

**Start Small**: Don't try to understand the entire ROM at once. Pick one specific feature (like how the flippers work or how bonus is calculated) and ask Claude to help you trace through just that code.

**Use Reference Materials**: Pinball technical manuals or schematics often include memory maps showing what RAM addresses and I/O ports do. You can share this information with Claude to help it provide better context: "According to the manual, the PIA U10 at 0x0088 handles the switch matrix. Can you show me where the code uses this?"

**Ask Follow-Up Questions**: If Claude's explanation uses terms you don't understand, just ask! "What's BCD format?" or "Can you explain what 'masking with 0x0F' means?"

**Document As You Go**: Take advantage of Claude's ability to update your Ghidra project. As soon as you understand something, have Claude rename it and add comments. Your future self will thank you when you come back to the analysis later.

**Compare Similar Games**: If you're stuck on something in one game, you can load a ROM from a similar game (same manufacturer, same era) and ask Claude to help you compare: "How is this different from how the other ROM handled it?"

**Build Context Gradually**: As you learn about different parts of the code, share that context with Claude in your questions: "Earlier we found that 0x00B6 is a game state flag. What's this code testing that flag for?"

**Use Claude's Overview Capabilities**: When you're trying to get oriented in a large ROM, ask Claude for high-level views: "Can you list all the functions and give me a brief description of what each one might do based on the code?" This can help you prioritize what to analyze in detail.

**Let Claude Track Cross-References**: When you find an interesting data structure or function, ask Claude: "Show me everywhere this is referenced in the code." This helps you understand how different parts of the program interact.

**Join the Community**: Share your findings on pinball forums. Others may have analyzed the same games and can confirm your interpretations or share additional insights.

## **Common Patterns in Pinball Code**

As you analyze more ROMs, you'll start recognizing common patterns. Claude can help you identify these:

    - **Debouncing loops**: Small timing delays after reading switches to ensure clean signals
    - **BCD arithmetic**: Scores are often stored in Binary Coded Decimal format for easy display
    - **Table lookups**: Arrays of values for sound pitches, lamp patterns, scoring values
    - **State machines**: Game modes tracked in specific RAM locations with code branching based on current state
    - **Interrupt-driven timing**: Many operations happen in response to periodic timer interrupts (often 60Hz or 120Hz)

You can ask Claude: "Is this a debouncing routine?" or "Can you identify the BCD addition happening here?"

## **Wrapping Up**

Analyzing vintage pinball ROMs might seem daunting at first, but with Ghidra, GhidraMC6800, GhidrassistMCP, and Claude Code working together, you can actually understand what's happening inside these classic machines. You don't need to be a professional reverse engineer—just curiosity, patience, and the willingness to ask questions.

The conversational interface makes all the difference. Instead of struggling to remember complex tool commands or spending hours researching assembly language references, you can simply select code and ask "What does this do?" Claude becomes your knowledgeable assistant, helping you unravel the mysteries of MC6800 assembly code one question at a time—and actively helping you document your discoveries as you go.

Whether you're trying to diagnose a problem with your game, understand how a particular feature was implemented, or just appreciate the clever programming that went into these machines with just a few KB of code, these tools open up a fascinating window into pinball history.

Start with something simple—maybe "Can you help me find where the start button is checked?"—and build from there. As you identify functions and understand their purpose, have Claude help you organize your analysis with clear names and comments. Before long, you'll be reading 6800 assembly code like a pro and discovering the secrets hidden in those ROM chips.

Happy analyzing, and may your multiballs be plentiful!

<hr />
Have you uncovered any interesting discoveries in your pinball ROM explorations? Share your findings on the forums—the community would love to hear about clever programming tricks or hidden features you've found!
