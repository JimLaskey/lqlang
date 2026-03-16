+++
date = '2026-03-16T10:03:50-03:00'
draft = false
title = 'The LQ Tool Chain'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "howto",  "LLM"]
+++

[Let the Robots Speak](/blog/let-the-robots-speak) introduced LQ — a visual dataflow language designed for AI agents. This post looks under the hood at how LQ programs go from graph to running code.

## Architecture

The LQ toolchain is built around a single persistent process: the **lqc server**. Both the native macOS IDE and (eventually) a browser-based client communicate with lqc through a message-based protocol. One server, multiple clients, same capabilities.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 100">
  <style>
    .wire { fill: none; stroke: #73726c; stroke-width: 0.5; }
    .client { fill: #e8f2fc; stroke: #1466b8; stroke-width: 0.5; }
    .server { fill: #f6fafe; stroke: #1466b8; stroke-width: 0.5; }
    .service { fill: #e8f2fc; stroke: #1466b8; stroke-width: 0.5; }
    .ext { fill: #e8f2fc; stroke: #1466b8; stroke-width: 0.5; }
    .title { font-family: Helvetica, Arial, sans-serif; font-size: 14px; fill: black; text-anchor: middle; }
    .sub { font-family: Helvetica, Arial, sans-serif; font-size: 12px; fill: black; text-anchor: middle; }
    .label { font-family: Helvetica, Arial, sans-serif; font-size: 12px; fill: #5f5e5a; text-anchor: middle; }
  </style>
 <g transform="scale(0.20)">
 <!-- Clients -->
  <rect class="client" x="100" y="30" width="180" height="56" rx="8"/>
  <text class="title" x="190" y="50">IDE</text>
  <text class="sub" x="190" y="70">Native macOS</text>
  <rect class="client" x="400" y="30" width="180" height="56" rx="8"/>
  <text class="title" x="490" y="50">Browser</text>
  <text class="sub" x="490" y="70">Pending</text>
  <!-- Client arrows -->
  <line class="wire" x1="190" y1="86" x2="190" y2="148"/>
  <line class="wire" x1="490" y1="86" x2="490" y2="148"/>
  <text class="label" x="340" y="128">Messages</text>
  <!-- lqc server container -->
  <rect class="server" x="60" y="150" width="560" height="182.5" rx="20"/>
  <text class="title" x="340" y="178">lqc server</text>
  <!-- Language service -->
  <rect class="service" x="80" y="200" width="150" height="80" rx="12"/>
  <text class="title" x="155" y="224">Language service</text>
  <text class="sub" x="155" y="246">Autocomplete</text>
  <text class="sub" x="155" y="264">Verify</text>
  <!-- Compiler -->
  <rect class="service" x="260" y="200" width="160" height="80" rx="12"/>
  <text class="title" x="340" y="224">Compiler</text>
  <text class="sub" x="340" y="246">Compile, build</text>
  <text class="sub" x="340" y="264">Bundle</text>
  <!-- Runtime -->
  <rect class="service" x="450" y="200" width="150" height="80" rx="12"/>
  <text class="title" x="525" y="224">Runtime</text>
  <text class="sub" x="525" y="246">Run</text>
  <text class="sub" x="525" y="264">Debug</text>
  <!-- Arrows to external tools -->
  <line class="wire" x1="288.91" y1="332.5" x2="245.67" y2="409.74"/>
  <line class="wire" x1="391.08" y1="332.5" x2="434.32" y2="409.74"/>
  <!-- LLVM -->
  <rect class="ext" x="140" y="409.74" width="180" height="56" rx="8"/>
  <text class="title" x="230" y="430">LLVM</text>
  <text class="sub" x="230" y="450">Native code generation</text>
  <!-- LLDB -->
  <rect class="ext" x="360" y="409.74" width="180" height="56" rx="8"/>
  <text class="title" x="450" y="430">LLDB</text>
  <text class="sub" x="450" y="450">Debug engine</text>
  </g>
</svg>

The lqc server handles three categories of work: language services, compilation, and runtime.

## Language Services

The language service is the real-time layer — the part that responds while the developer (or agent) is still editing.

**Autocomplete** examines the current graph context — the types flowing through nearby connections, the available methods on those types — and suggests completions. Because LQ programs are typed graphs rather than text files, completions are structurally constrained. The system doesn't guess what token might come next in a stream of characters. Instead, the system knows what types arrive at a terminal and offers only the methods that accept those types.

**Verification** runs continuously in the background, checking the graph for type mismatches, disconnected terminals, unreachable closures, and structural/expression errors. Diagnostics are reported with node and terminal IDs, so the IDE can highlight exactly where the problem is — not a line number, but a specific node in the graph.

## Compiler

When the graph is ready to build, the compiler takes over.

**Compilation** translates the LQ graph into LLVM IR. This is where the dataflow semantics become concrete: independent branches in the graph map to parallel execution paths, data dependencies become explicit in the IR, and the type system is fully resolved. LLVM then handles optimization and native code generation for the target platform.

**Building** links compiled modules into executables or libraries. Because the code generation and libraries are all in LLVM bitcode, optimization (ex. inlining) can span compile units.

**Bundling** packages the result for deployment — whether that's a standalone binary, a library, or an agent-consumable artifact.

The choice of LLVM is deliberate. LQ programs compile to the same native code that C, C++, Rust, and Swift produce. No interpreter. No VM. No runtime overhead from the visual representation — the graph is a compile-time construct that dissolves into efficient machine code.

## Runtime

**Running** an LQ program launches the compiled binary under the server's supervision, with the ability to stream output and status back to the IDE.

**Debugging** uses LLDB — the same debug engine behind Xcode and the LLVM project. Breakpoints, stepping, variable inspection — all mapped back to nodes and connections in the graph rather than lines in a text file. When execution pauses, the IDE highlights the active node and shows data values on the connections flowing into and out of it.

This is one of the advantages of a graph-based representation for debugging. A text debugger shows a call stack — a linear trace through nested function calls. An LQ debugger shows the *topology* — which branches are active, which are waiting, where data has flowed and where it hasn't. The state of the program is visible as the state of the graph.

## One Server, Any Client

The lqc server doesn't care what's on the other end of the message protocol. Today that's a native macOS IDE built in SwiftUI. Tomorrow it could be a browser-based editor, a command-line tool, or an AI agent sending messages programmatically.

This is the same pattern that made LSP (Language Server Protocol) successful — decouple the language intelligence from the editor. The difference is that LQ's protocol is richer than LSP because the language is richer than text. Verify doesn't return squiggly underlines on a line range — it returns diagnostics on specific nodes and terminals. Autocomplete doesn't suggest text insertions — it suggests typed method invocations with compatible signatures.

The protocol is the contract. The client is interchangeable.

## What's Next

Upcoming posts will dig deeper into specific layers — the type system, how closures work in a dataflow graph, the agent serialization format that lets AI write LQ programs directly, and how control flow emerges from combining simple visual primitives.

The toolchain is the foundation. Everything else builds on top of it.
