# Minecraft AI Dataset

An open, versioned dataset project for Minecraft knowledge and Minecraft vision-language-action (VLA) research. It is designed to support two complementary training styles inspired by the CraftJarvis releases:

- [minecraft-textvla-qwen2vl-7b-2509](https://huggingface.co/CraftJarvis/minecraft-textvla-qwen2vl-7b-2509): visual observations paired with language instructions, descriptions, answers, and actions.
- [minecraft-motionha-qwen2vl-7b-2509](https://huggingface.co/CraftJarvis/minecraft-motionha-qwen2vl-7b-2509): observations paired with motion/action labels and hierarchical task trajectories.

This repository is a **new, independently prepared dataset**. It does not redistribute CraftJarvis model weights or copy restricted training data. It uses publicly documented Minecraft information and clearly records provenance for every example.

## Scope

The project will contain:

- Wiki-style factual knowledge: blocks, items, mobs, biomes, dimensions, structures, recipes, effects, enchantments, commands, and mechanics.
- Instruction and question-answer examples: how to craft, obtain, use, build, survive, explore, and troubleshoot.
- Screenshot-grounded examples: visual descriptions, UI interpretation, object identification, and next-action suggestions.
- Motion/action examples: atomic controls, action sequences, goals, preconditions, termination conditions, and failure recovery.
- Version-aware records for Java Edition and Bedrock Edition, including edition-specific differences.

## Version policy

Every record must include:

- `edition`: `java`, `bedrock`, or `both`
- `minecraft_version`: the exact release or version range
- `source_url`
- `retrieved_at`
- `verification_status`

The initial target is **Minecraft Java Edition 26.3**, with 26.2 compatibility where applicable. Version-specific claims must be checked against official release notes and the current [Minecraft Wiki](https://minecraft.wiki/). Do not assume a feature exists in every edition or version.

## Planned layout

```text
minecraft-ai-dataset/
├── README.md
├── LICENSE
├── CHANGELOG.md
├── datasets/
│   ├── textvla/
│   │   ├── knowledge_qa.jsonl
│   │   ├── recipes_and_commands.jsonl
│   │   ├── visual_grounding.jsonl
│   │   └── instruction_following.jsonl
│   ├── motionha/
│   │   ├── atomic_actions.jsonl
│   │   ├── hierarchical_tasks.jsonl
│   │   ├── trajectories.jsonl
│   │   └── recovery_and_failures.jsonl
│   └── unified/
│       └── minecraft_v26.3.jsonl
├── schemas/
│   ├── textvla.schema.json
│   ├── motionha.schema.json
│   └── annotation_guidelines.md
├── tools/
│   ├── validate_jsonl.py
│   └── build_unified.py
└── sources/
    └── source_manifest.jsonl
```

## Record examples

### TextVLA / knowledge example

```json
{
  "id": "java-26.3-crafting-000001",
  "task_type": "knowledge_qa",
  "edition": "java",
  "minecraft_version": "26.3",
  "input": {
    "question": "How do I craft a wooden pickaxe?",
    "image": null
  },
  "output": {
    "answer": "Use a crafting table. Place three matching wooden planks across the top row and two sticks in the center column below them.",
    "actions": ["open_crafting_table", "place_items", "take_result"]
  },
  "source_url": "https://minecraft.wiki/",
  "retrieved_at": "2026-09-29",
  "verification_status": "needs_source_verification",
  "tags": ["crafting", "tools", "early_game"]
}
```

### MotionHA / hierarchical task example

```json
{
  "id": "java-26.3-task-000001",
  "task_type": "hierarchical_task",
  "edition": "java",
  "minecraft_version": "26.3",
  "goal": "Prepare basic stone tools",
  "subtasks": [
    {"step": 1, "action": "collect", "target": "wood_logs", "success": "has_planks_and_sticks"},
    {"step": 2, "action": "craft", "target": "crafting_table", "success": "table_placed_or_in_inventory"},
    {"step": 3, "action": "mine", "target": "stone", "tool": "wooden_pickaxe", "success": "has_cobblestone"},
    {"step": 4, "action": "craft", "target": "stone_tools", "success": "stone_pickaxe_or_axe_available"}
  ],
  "termination": "player has the requested tools",
  "failure_recovery": ["replace_missing_tool", "move_to_safe_location"],
  "source_url": "https://minecraft.wiki/",
  "retrieved_at": "2026-09-29",
  "verification_status": "needs_source_verification"
}
```

## Data quality and licensing

- Do not present generated examples as verified facts until they have been checked.
- Keep Java and Bedrock behavior separate when they differ.
- Preserve source URLs and retrieval dates; do not silently overwrite changed facts.
- Remove personal data, server credentials, copyrighted screenshots without permission, and mod content unless explicitly labeled and licensed.
- Minecraft and its assets are owned by Microsoft/Mojang. This repository is an independent research project and is not endorsed by Mojang.
- Each contributed file must include its applicable license and provenance information.

## Loading JSONL with Hugging Face Datasets

```python
from datasets import load_dataset

textvla = load_dataset(
    "json",
    data_files="datasets/textvla/knowledge_qa.jsonl",
    split="train",
)
motionha = load_dataset(
    "json",
    data_files="datasets/motionha/hierarchical_tasks.jsonl",
    split="train",
)
```

## Sources and related projects

- [Minecraft Wiki](https://minecraft.wiki/)
- [Minecraft Java Edition release notes](https://www.minecraft.net/en-us/article)
- [CraftJarvis TextVLA model](https://huggingface.co/CraftJarvis/minecraft-textvla-qwen2vl-7b-2509)
- [CraftJarvis MotionHA model](https://huggingface.co/CraftJarvis/minecraft-motionha-qwen2vl-7b-2509)
- [CraftJarvis motion-action dataset](https://huggingface.co/datasets/CraftJarvis/minecraft-motion-action-dataset)
- [CraftJarvis OpenHA](https://github.com/CraftJarvis/OpenHA)

## Status

The repository currently contains the project README. The next implementation step is to add the schemas, source manifest, validation tools, and verified JSONL records before claiming that a dataset split is complete.
