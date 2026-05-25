# Dataset Preview

Here are three raw examples pulled directly from the newly generated JSONL dataset. Notice how they are formatted in the standard ChatML `messages` format, which is the industry standard for fine-tuning models like Llama-3 and Gemma.

````carousel
## Example 1: Stack Overflow (Troubleshooting Q&A)
This example teaches the model how to respond to conceptual and debugging questions by mapping an engineer's question to a verified accepted answer.

```json
{
  "messages": [
    {
      "role": "user", 
      "content": "PLC Version Control\n\nI need to come up with a CM process for PLC code.\n\nCurrently, the system is developed using RSLogix 5000. The build product is a monolithic file that can be loaded onto a PLC for execution and edited directly in the development environment. With multiple developers, this has become a problem. They're stepping on each others changes.\n\nHow can I coordinate changes between multiple developers working with PLC's?"
    }, 
    {
      "role": "assistant", 
      "content": "If we are talking about one big binary files, then a VCS (centralized or decentralized) is not the best tool for the job. An external referencial (a shared disk for instance) where a batch will copy and label the current PCL state is better.\n\nTo avert discontinuities in the historical record of revisions, old versions of programs must be stored. We take it a step further, though. Using our MDT AutoSave, we actually go out and interrogate the equipment. Overnight or at whatever frequency is specified, the software reads the programs in the PLCs and then compares that information to the last known program...\n\nThat being said, when you have multiple changes from multiple developers, you need an integration environment where a first delivery can be done and validated, before pushing it to the actual server."
    }
  ]
}
```

<!-- slide -->
## Example 2: PLC-BEAD Open Source (Code Generation)
This is an example from the academic dataset. The model is given a prompt to complete a file, and the assistant provides the exact, compilable Structured Text.

```json
{
  "messages": [
    {
      "role": "user", 
      "content": "Complete the following PLC Structured Text program for ACOSH.ST:"
    }, 
    {
      "role": "assistant", 
      "content": "FUNCTION ACOSH:REAL\n\tVAR_INPUT\n\t\tX :\tREAL;\n\tEND_VAR\n\tACOSH := LN(SQRT(X * X - 1.0) + X);\n\nEND_FUNCTION\n\nPROGRAM program0\n  VAR\n    LocalVar0 : REAL;\n    LocalOUT  : REAL;\n  END_VAR\n\n  LocalOUT := ACOSH(LocalVar0);\nEND_PROGRAM\n"
    }
  ]
}
```

<!-- slide -->
## Example 3: GitHub Raw Scrape (Raw Proprietary Syntax)
This is an example scraped via your GitHub PAT. 

> [!WARNING]
> **Data Cleaning Note:** Because we scraped raw extensions, some files (like `.scl`) occasionally collide with other languages (like Scala or Starlark). You can see below that this file is actually a Bazel build script. In a production pipeline, we would add a fast regex or AST filtering step to drop files that don't look like valid PLC logic!

```json
{
  "messages": [
    {
      "role": "user", 
      "content": "Complete the following PLC logic from file project_proto.scl:"
    }, 
    {
      "role": "assistant", 
      "content": "# DO NOT EDIT!\n\"\"\"Generated with Protolark.\"\"\"\n\ndef _mk_pb2_third_party_bazel_src_main_protobuf_project_buildable_unit_BuildableUnit(name = [], target_patterns = [], flags = [], description = [], is_default = []):\n    return struct(**{\n        \"name\": name,\n        \"target_patterns\": target_patterns,\n        \"flags\": flags,\n        \"description\": description,\n        \"is_default\": is_default,\n    })\n\npb2_third_party_bazel_src_main_protobuf_project_buildable_unit = struct(\n    BuildableUnit = struct(\n        create = _mk_pb2_third_party_bazel_src_main_protobuf_project_buildable_unit_BuildableUnit,\n    ),\n)"
    }
  ]
}
```
````
