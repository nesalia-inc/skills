# Marty Content Rules

These rules apply to all training content created or improved under `/skills/content/courses`.

## Fundamental Principle: Facts, Not Opinions

### We Explain Facts

When explaining a feature, function, or concept, we explain **how it works** and **what it is**:

- What is this?
- What does it do?
- How does it work?
- What are its parts?

### We Don't Give Opinions

We don't say what someone "should" or "should not" do. We describe what **we** do.

```
❌ BAD:
You should always validate user input.
You should never use eval().

✅ GOOD:
We validate all user input at the API boundary.
We use JSON.parse() instead of eval() for security.
```

### When to Use "We" vs "You"

| Use | Instead of |
|-----|------------|
| "We do X" | "You should do X" |
| "We use X" | "Use X" or "You should use X" |
| "We prefer X" | "X is better" or "You should prefer X" |

## Rule 1: Knowledge is a Graph, Not a Line

**Every concept connects to other concepts.** Never teach in isolation. Always show relationships.

### Decompose Until Atomic

Break every concept into its smallest meaningful parts. Then break those parts further. Repeat until each piece is trivially understandable.

**Ask yourself:**
- "Is there anything I assume here that isn't explained?"
- "Could a complete beginner understand this without Googling?"
- "Is this sentence/paragraph doing one thing, or many?"
- "If I asked 'why?' three times, would I still have an answer?"

```
Example of decomposition:

❌ "Functions are reusable blocks of code"
   → Assumes: what is a block? what does reusable mean?

✅ "A function is a named, reusable block of code.

   What is a block of code?
   → A block is a group of statements that execute together.

   What does 'named' mean?
   → The function has a label you can use to call it elsewhere.

   What does 'reusable' mean?
   → You can call it multiple times from different places."
```

### Course Context: Reference Previous Parts

**In a formation (course), you don't re-explain everything.** The course builds progressively.

| Situation | Action |
|-----------|--------|
| Concept already covered in Part 1 | Reference: "As you learned in Part 1..." |
| Prerequisite not in this course | Link: "Learn more about X in Course Y" |
| New concept in this part | Explain fully with decomposition |
| Cross-part connection | Always make explicit: "This relates to..." |

```
Example:

# Course: Python Fundamentals

## Part 3: For Loops

We now know about lists (Part 1) and functions (Part 2).
This lets us teach for loops more efficiently:

✅ GOOD:
## For Loops

You already know:
- Lists store multiple items (Part 1)
- Functions are named reusable blocks (Part 2)

Now we combine these: a for loop executes a function for each item in a list.

### This Connects To
- While loops (Part 5) - another way to repeat
- List comprehensions (Part 4) - a concise for loop pattern
```

### Self-Check Questions

Before finishing any section, ask:
1. "Would a complete beginner understand this from scratch?"
2. "Did I assume any prerequisite knowledge?"
3. "Is there anything that could be clearer?"
4. "Did I connect this to other concepts?"
5. "Is every sentence/paragraph doing exactly one thing?"

### Knowledge as a Graph

```
                    ┌─────────────┐
                    │  Iteration  │
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
        ┌──────────┐ ┌─────────┐ ┌──────────┐
        │  Lists   │ │ Strings │ │  Ranges  │
        └──────────┘ └─────────┘ └──────────┘
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   for loop  │
                    └─────────────┘
```

**Never teach "for loops in Python" without first explaining:**
- What is iteration? (conceptual)
- Why do we need iteration? (problem it solves)
- What is a sequence? (lists, strings, ranges - prerequisite concepts)

### Decompose Everything to the Simplest Level

Break every concept down until nothing is assumed:

```
❌ BAD (0/10 - assumes too much):
## For Loops in Python
The for loop is used to iterate over sequences.

for item in sequence:
    # code

✅ GOOD (10/10 - builds from fundamentals):
## For Loops

### What is Iteration?

Iteration is the act of repeating a process. Each repetition is called an "iteration."

### Why Do We Need Iteration?

Imagine you have a list of 1000 users and need to send each one an email. Writing 1000 lines of code would be insane. Iteration lets us repeat an action for each item automatically.

### What is a Sequence?

A sequence is an ordered collection of items. Python has several built-in sequences:

| Sequence Type | Example | What it contains |
|---------------|---------|------------------|
| List | `[1, 2, 3]` | Ordered items (can change) |
| String | `"hello"` | Ordered characters |
| Range | `range(5)` | Ordered numbers (generated) |

### What Does "for" Mean?

The word "for" in English means "during the time that" or "in preparation for." In programming, "for" means: "DO this for EACH item in a collection."

### The for Loop Syntax

```
for item in collection:
    # do something with item
```

- `for` - keyword that starts iteration
- `item` - variable that holds each item during iteration
- `in` - keyword that separates variable from collection
- `collection` - the sequence to iterate over
- `:` - ends the line, starts the body
- indentation - defines the body (what runs for each item)

### Visualizing Execution

```
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)

# Execution flow:
# Iteration 1: fruit = "apple"  → print("apple")
# Iteration 2: fruit = "banana" → print("banana")
# Iteration 3: fruit = "cherry" → print("cherry")
```

### Connections to Other Concepts

- **Related:** While loops (another way to iterate - see "While Loops" section)
- **Prerequisite:** Lists, Strings, Ranges (must understand sequences first)
- **Next:** List comprehensions (a concise way to iterate)
```

### How to Apply This Rule

When writing any content:

1. **Identify prerequisite concepts** - What must the learner know before this?
2. **Always explain the "why"** - Not just "how", but "why would I use this?"
3. **Make explicit connections** - "This relates to X you learned in Y"
4. **Never skip fundamentals** - If explaining a for loop, don't assume they know what a sequence is
5. **Use visual explanations** - Diagrams, flow, step-by-step execution

```
❌ BAD (teaches syntax without understanding):
## For Loops
Use for to iterate over lists:
for i in range(5):
    print(i)

✅ GOOD (builds understanding from concepts):
## For Loops

### What is a Loop?
A loop repeats code. A for loop repeats once for each item in a sequence.

### When to Use for Loops
When you need to: process every item in a collection, repeat an action N times, transform each item in a list.

### How for Differs from while
- for: "Do this for each item" (known number of iterations)
- while: "Do this while condition is true" (unknown number of iterations)

See also: While Loops (to understand both loop types)
```


## Rule 2: Authoritative Voice

Write as if you are the **definitive source**. Use "We do X" not "Here is one way to do X" or "Some people do X".

```
❌ BAD:
Here is how you might set up authentication in this project.
Some teams prefer JWT, others prefer OAuth.

✅ GOOD:
We use OAuth 2.0 for authentication.
```

**Why:** Learners need clear guidance, not options. Confidence comes from knowing the right way to do things.

## Rule 3: Forward-Looking Only

**Never include:**
- "Previously we did X"
- "This was changed in v2.0"
- "Originally, we thought X but now"
- "Previously..."
- "Before..."
- "Old version:"

**Rewrite old content to present current truth, not history.**

```
❌ BAD:
We previously used JWT tokens. Now we use OAuth 2.0.

✅ GOOD:
We use OAuth 2.0 for authentication.
```

**Why:** Documentation is not a changelog. Learners need to know how things work now, not how they evolved.

## Rule 4: Complete Information

A good tutorial doesn't leave gaps. Every step should be:

- **Explicit** (not implied)
- **Actionable** (can follow without guessing)
- **Complete** (no missing context)

```
❌ BAD:
1. Install the package
2. Configure it
3. Run the tests

✅ GOOD:
1. Install the package: npm install @company/auth
2. Configure it by creating auth.config.js with your client ID and secret
3. Run the tests: npm test -- --grep "auth"
```

**Why:** Learners get stuck on implied steps. Complete instructions prevent frustration.

## Rule 5: Practical Examples

Include real code, real commands, real outputs. Not placeholder examples.

```
❌ BAD:
// Example of a user object
const user = {
  name: "John",
  email: "john@example.com"
};

✅ GOOD:
const user = {
  id: "usr_abc123",
  name: "Alice Johnson",
  email: "alice@company.io",
  role: "admin",
  createdAt: new Date("2024-01-15T10:30:00Z")
};
```

**Why:** Real examples prepare learners for real work. Placeholders don't transfer to actual implementation.

## Rule 6: Progressive Complexity

Structure content from simple to complex:

1. **Concepts** (what and why)
2. **Basic usage** (first steps)
3. **Advanced usage** (complex scenarios)
4. **Reference** (detailed API)

```
❌ BAD:
## Authentication
We support JWT, OAuth, API keys, SAML, and LDAP. Let's start with LDAP configuration...

✅ GOOD:
## Authentication

### Concepts
We use OAuth 2.0 for user authentication.

### Basic Usage
1. Initialize the auth client
2. Authenticate user credentials
3. Receive access token

### Advanced Usage
For enterprise scenarios with SAML or LDAP, see the Enterprise Auth guide.

### Reference
See API Reference → Authentication for complete endpoint documentation.
```

**Why:** Starting complex overwhelms beginners. Building up enables mastery.

## Rule 7: Hands-On Exercises

Include exercises that let learners practice. Learning by doing is more effective than reading alone.

Include:
- Code-along examples (learn while building)
- Challenge problems (apply what you learned)
- Real-world scenarios (connect to actual work)

**Why:** Passive reading doesn't create skills. Active practice does.
