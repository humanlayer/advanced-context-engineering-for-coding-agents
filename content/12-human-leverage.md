[← Back to README](../README.md)

## Human Leverage: Where to Focus Your Attention

### The Leverage Pyramid

If there's one thing you take away from all this, let it be this:

**A bad line of code** = a bad line of code  
**A bad line of plan** = hundreds of bad lines of code  
**A bad line of research** = thousands of bad lines of code

<img width="225" height="728" alt="Leverage pyramid" src="https://github.com/user-attachments/assets/398ac859-80c6-4791-8c61-4c13198ee6f6" />

### The Review Strategy

Focus human effort on the HIGHEST LEVERAGE parts of the pipeline:

<img width="1331" height="745" alt="Human review points" src="https://github.com/user-attachments/assets/305d3716-cb5c-4c1d-bb2b-bc035b35540b" />

#### What to Review in Research

<img width="1333" height="749" alt="Research review" src="https://github.com/user-attachments/assets/85b6a4c1-71e1-4deb-afef-5a797944a3ac" />

- Is the understanding correct?
- Are the key files identified?
- Is the flow accurately traced?
- Are the assumptions valid?

#### What to Review in Plans

<img width="1333" height="746" alt="Plan review" src="https://github.com/user-attachments/assets/54a09c99-b177-41b2-a43d-04d6b94bc56e" />

- Will this approach work?
- Are the phases properly ordered?
- Are the test strategies sufficient?
- Are there edge cases missed?

#### What to Review in Implementation

- Are the tests passing?
- Does it match the plan?
- Are there obvious issues?

Notice how the review gets lighter as you go down? That's intentional.

### The Time Investment

Reading 200 lines of plan > Reading 2000 lines of code

But more importantly:

Fixing a bad plan before implementation > Fixing bad code after implementation

### This Is Not Magic

<img width="1331" height="748" alt="Not magic, engineering" src="https://github.com/user-attachments/assets/f12a10e2-7ffe-44c5-9d9a-b6e42ff5251e" />

Remember when I threw away that first research because it was wrong? That's the human leverage point. No amount of prompt engineering would have caught that. It required understanding the problem domain.

You MUST:
- Read the research critically
- Challenge the plans
- Verify the assumptions
- Know when to intervene

The system amplifies human judgment. It doesn't replace it.

[← Real World: BAML](11-real-world-baml.md) | [Code Review in the Age of AI →](13-code-review-mental-alignment.md)