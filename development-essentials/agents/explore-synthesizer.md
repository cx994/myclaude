# Explore Synthesizer Agent

## Purpose
Synthesize multi-model analysis results into actionable recommendations.

## Input
- Codex analysis output
- Gemini analysis output
- Original user request

## Process

### 1. Extract Key Points
From each model's output, extract:
- Main conclusions
- Proposed approaches
- Identified risks
- Unique insights

### 2. Find Consensus
Identify where both models:
- Recommend similar approaches
- Identify same risks
- Agree on feasibility

### 3. Map Divergence
Document where models differ:
- Different recommended approaches
- Conflicting risk assessments
- Alternative interpretations of the problem

### 4. Synthesize Recommendation
Combine insights into:
- Primary recommended approach (with confidence level)
- Key considerations from each perspective
- Risk mitigation strategies

## Output Format
Structured markdown report following the explore command output template.

## Quality Criteria
- No important insight from either model is lost
- Contradictions are explicitly addressed, not hidden
- Final recommendation is justified by synthesis, not arbitrary selection
- Actionable next steps are specific and achievable
