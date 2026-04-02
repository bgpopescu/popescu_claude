---
name: humanizer
disable-model-invocation: true
description: "Remove AI writing patterns from academic text. Catches inflated significance, vague attributions, hedging, and promotional language. Use after drafting prose with Claude."
argument-hint: "[file-path-or-text]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
---

# Humanizer: Remove AI Writing Patterns from Academic Text

Identify and remove signs of AI-generated text, focused on patterns that matter in academic writing. Based on Wikipedia's "Signs of AI writing" guide (WikiProject AI Cleanup), adapted for research prose.

## Your Task

1. **Identify AI patterns** — scan for the patterns listed below
2. **Rewrite problematic sections** — replace AI-isms with natural alternatives
3. **Preserve meaning** — keep the core message intact
4. **Add soul** — don't just remove bad patterns; inject actual personality
5. **Final anti-AI pass** — ask "What makes this obviously AI generated?" then revise

## Personality and Soul

Avoiding AI patterns is only half the job. Sterile, voiceless writing is just as obvious as slop.

### Signs of soulless academic writing:

- Every sentence is the same length and structure
- No active voice — everything is passive and impersonal
- Vague where it should be concrete
- No acknowledgment of genuine uncertainty or limitations
- Reads like it was assembled from templates

### How to add voice in academic prose:

**Vary your rhythm.** Short declarative sentences. Then longer ones that develop an argument across a clause or two. Monotonous sentence length is a strong AI tell.

**Use active voice where appropriate.** "We estimate" is better than "estimates were obtained." Active voice is clearer and more direct — most style guides prefer it.

**Be concrete, not vague.** Not "this has important implications" but "this suggests that trade policy affects wages through labor reallocation, not firm exit."

**Acknowledge genuine uncertainty.** "The estimates are imprecise and we cannot rule out a null effect" is more credible than hedging everything with "might possibly suggest."

## Patterns

### 1. Inflated Significance and Legacy

**Watch for:** stands/serves as, is a testament/reminder, pivotal/crucial/key role, underscores/highlights its importance, reflects broader, symbolizing, setting the stage for, evolving landscape, indelible mark

**Before:** > The Statistical Institute of Catalonia was officially established in 1989, marking a pivotal moment in the evolution of regional statistics in Spain.

**After:** > The Statistical Institute of Catalonia was established in 1989 to collect and publish regional statistics independently from Spain's national statistics office.

### 2. Superficial -ing Analyses

**Watch for:** highlighting/underscoring/emphasizing..., ensuring..., reflecting/symbolizing..., contributing to..., showcasing...

**Before:** > The temple's color palette resonates with the region's natural beauty, symbolizing Texas bluebonnets, showcasing how these elements have integrated into the traditional aesthetic.

**After:** > The temple uses blue, green, and gold. The architect said these were chosen to reference local bluebonnets and the Gulf coast.

### 3. Promotional Language

**Watch for:** boasts a, vibrant, rich (figurative), profound, showcasing, exemplifies, commitment to, groundbreaking, renowned, breathtaking, stunning

**Before:** > Nestled within the breathtaking region of Gonder, Alamata stands as a vibrant town with a rich cultural heritage and stunning natural beauty.

**After:** > Alamata is a town in the Gonder region of Ethiopia, known for its weekly market and 18th-century church.

### 4. Vague Attributions and Weasel Words

**Watch for:** Industry reports, Observers have cited, Experts argue, Some critics argue, several sources/publications

**Before:** > Experts believe it plays a crucial role in the regional ecosystem.

**After:** > The Haolai River supports several endemic fish species, according to a 2019 survey by the Chinese Academy of Sciences.

### 5. Formulaic "Challenges and Future Prospects"

**Watch for:** Despite its... faces several challenges..., Despite these challenges, Future Outlook

**Before:** > Despite its industrial prosperity, Korattur faces challenges typical of urban areas. Despite these challenges, Korattur continues to thrive.

**After:** > Traffic congestion increased after 2015 when three new IT parks opened. The municipal corporation began a stormwater drainage project in 2022.

### 6. Overused AI Vocabulary

**High-frequency words:** Additionally, align with, crucial, delve, emphasizing, enduring, enhance, fostering, garner, highlight (verb), interplay, intricate, key (adjective), landscape (abstract), pivotal, showcase, tapestry (abstract), testament, underscore, valuable, vibrant

**Before:** > Additionally, a distinctive feature is the incorporation of camel meat. An enduring testament to Italian colonial influence is the widespread adoption of pasta in the local culinary landscape.

**After:** > Somali cuisine also includes camel meat. Pasta dishes, introduced during Italian colonization, remain common in the south.

### 7. Negative Parallelisms

**Watch for:** Not only...but..., It's not just about..., it's...

**Before:** > It's not merely a song, it's a statement.

**After:** > The heavy beat adds to the aggressive tone.

### 8. Formulaic Rule of Three

Tripling is a legitimate rhetorical device — the problem is *mechanical* tripling where three nouns are forced together for false comprehensiveness.

**Before:** > The event features keynote sessions, panel discussions, and networking opportunities. Attendees can expect innovation, inspiration, and industry insights.

**After:** > The event includes talks and panels. There's also time for informal networking between sessions.

### 9. Filler Phrases

- "In order to achieve this goal" → "To achieve this"
- "Due to the fact that" → "Because"
- "At this point in time" → "Now"
- "It is important to note that the data shows" → "The data shows"
- "The system has the ability to process" → "The system can process"

### 10. Excessive Hedging

**Before:** > It could potentially possibly be argued that the policy might have some effect on outcomes.

**After:** > The policy may affect outcomes.

### 11. Generic Positive Conclusions

**Before:** > The future looks bright for the company. Exciting times lie ahead as they continue their journey toward excellence.

**After:** > The company plans to open two more locations next year.

## Process

1. Read the input text carefully
2. Identify all instances of the patterns above
3. Rewrite each problematic section
4. Ensure the revised text sounds natural when read aloud
5. Present a draft rewrite
6. Ask: "What makes this obviously AI generated?" — answer briefly
7. Revise remaining tells
8. Present the final version

## Output

1. Draft rewrite
2. Remaining AI tells (brief bullets)
3. Final rewrite

## Reference

Based on [Wikipedia:Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), maintained by WikiProject AI Cleanup. Original skill by Siqi Chen, adapted for academic writing.
