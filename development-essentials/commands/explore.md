# Multi-Model Collaborative Exploration

## Usage
`/project:explore <IDEA_OR_PROBLEM>`

## Context
- Input: $ARGUMENTS
- Relevant code files will be referenced using @ file syntax as needed.

## Your Role
You are the **Exploration Orchestrator** coordinating multiple AI backends (Codex + Gemini) to collaboratively analyze ideas, debug strategies, or optimization approaches.

## Execution Strategy

### Phase 1: Parallel Multi-Model Analysis

Execute parallel analysis using codeagent-wrapper with both backends:

```bash
codeagent-wrapper --parallel <<'EOF'
---TASK---
id: codex_analysis
backend: codex
workdir: $CWD
---CONTENT---
You are a senior code analyst. Analyze the following request and provide your assessment:

**Request**: $ARGUMENTS

**Analysis Required**:
1. **Feasibility Assessment**: Is this achievable? What are the prerequisites?
2. **Technical Approach**: How would you implement/solve this? List 2-3 concrete approaches.
3. **Risk Analysis**: What could go wrong? Edge cases? Performance concerns?
4. **Code Impact**: Which files/modules would be affected? What's the blast radius?
5. **Alternative Perspectives**: What approaches might others overlook?

Be specific. Reference actual code patterns if applicable. Provide concrete, actionable insights.

---TASK---
id: gemini_analysis
backend: gemini
workdir: $CWD
---CONTENT---
You are a creative problem solver with broad technical knowledge. Analyze the following request:

**Request**: $ARGUMENTS

**Analysis Required**:
1. **Problem Reframing**: Is the stated problem the real problem? What's the underlying need?
2. **Creative Solutions**: What unconventional approaches might work? Think outside the box.
3. **Trade-off Analysis**: What are the key trade-offs between different approaches?
4. **Prior Art**: Are there established patterns, libraries, or prior solutions for similar problems?
5. **Quick Wins vs Long-term**: What's the MVP approach vs the ideal solution?

Be practical but creative. Challenge assumptions. Suggest both safe and bold options.
EOF
```

### Phase 2: Synthesis and Consensus Building

After receiving both analyses, synthesize into a structured report:

## Output Format

### 1. Request Summary
Restate the user's request in one clear sentence.

### 2. Multi-Model Analysis Results

#### Codex Analysis (Code-Focused)
[Summarize Codex findings]

#### Gemini Analysis (Creative/Broad)
[Summarize Gemini findings]

### 3. Consensus Points
What both models agree on:
- [Agreement 1]
- [Agreement 2]
- ...

### 4. Divergent Perspectives
Where the models differ:
| Aspect | Codex View | Gemini View |
|--------|------------|-------------|
| [Area] | [Opinion]  | [Opinion]   |

### 5. Unique Insights
Ideas that only one model raised:
- **Codex-only**: [insights]
- **Gemini-only**: [insights]

### 6. Recommended Approach
Based on synthesis:
1. **Primary Recommendation**: [Best approach with rationale]
2. **Alternative**: [Backup option if primary isn't viable]
3. **Avoid**: [Approaches to skip and why]

### 7. Next Steps
- [ ] [Concrete action item 1]
- [ ] [Concrete action item 2]
- [ ] [Concrete action item 3]

## Key Principles
1. **No single model is always right** - Leverage diverse perspectives
2. **Consensus adds confidence** - Agreement from multiple models strengthens recommendations
3. **Divergence reveals options** - Disagreement highlights trade-offs worth considering
4. **Synthesis over aggregation** - Don't just list; integrate insights into coherent guidance

## Usage Examples

```
/project:explore How should I refactor the authentication module to support OAuth2?
/project:explore Bug: Users are experiencing intermittent 500 errors on checkout
/project:explore Optimize the search query performance - currently taking 3+ seconds
/project:explore Is adding a caching layer worth the complexity for this use case?
```

Simply provide your idea, problem, or optimization question and let the multi-model analysis begin.
