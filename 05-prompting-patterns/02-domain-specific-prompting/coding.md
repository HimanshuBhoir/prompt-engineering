## Domain-Specific Prompting

Prompting changes drastically across domains. What works for code generation fails for teaching. What works for research doesn't work for product management. Here's how to adapt prompting for each major domain.

### Coding

**The unique challenge:** Code completion models are trained on code files from GitHub, but developers write code differently than completed files in repos - they jump around, write in hierarchical order (signatures first, then bodies), and often have incomplete code that doesn't compile.

**Key principles for coding prompts:**

**1. Specify the programming language explicitly**

If you don't specify the language, LLMs default to whatever is most popular in their training data - usually Python for general tasks or JavaScript for web-related tasks.

```
BAD:
"Write a function to sort an array"

GOOD:
"Write a Python function using quicksort to sort an array of integers in ascending order"
```

**2. Provide context about the codebase**

```
Context: We're building a React e-commerce app using TypeScript, Redux for state management, and styled-components for styling.

Task: Create a reusable ProductCard component that displays product image, name, price, and an "Add to Cart" button. The button should dispatch a Redux action when clicked.
```

Clear thinking + clear communication = effective prompting = effective code. Break down tasks into chunks the model can handle.

**3. Request auxiliary tasks beyond code generation**

Prompt models to perform additional tasks like debugging, explaining, writing tests, or documenting code - this improves output quality.

```
Write a Python function to validate email addresses using regex.

Then:
1. Write unit tests covering valid emails, invalid formats, and edge cases
2. Add docstring documentation explaining parameters and return values
3. Include inline comments for complex regex patterns
4. Suggest potential improvements or security considerations
```

**4. For multi-file projects, request scaffolding scripts**

When generating code spanning multiple files, prompt the model to create a script that automatically creates specified files or modifies existing ones.

```
I'm building a Flask API with user authentication. Generate:
1. The complete code for all necessary files (app.py, models.py, auth.py, config.py)
2. A Python script that creates this file structure and populates each file
3. A requirements.txt with all dependencies

Structure:
```
project/
  ├── app.py
  ├── models.py
  ├── auth.py
  └── config.py
```
```

**5. Specify code style and formatting guidelines**

Many companies and projects have specific style guides - provide these to ensure generated code follows your conventions.

```
Generate a C# class for user management following these conventions:
- Use PascalCase for class and method names
- Private fields start with underscore (_userId)
- Use explicit access modifiers
- Follow Microsoft's C# coding conventions
- Maximum line length: 120 characters
- Use XML documentation comments for public methods
```

**6. Use Chain-of-Thought for complex algorithms**

```
Implement a binary search tree with insert, delete, and search operations.

Think through this step-by-step:
1. First, explain the data structure and node representation
2. Then, describe the algorithm for each operation
3. Finally, implement the code with detailed comments

For each operation, explain:
- Time complexity
- Edge cases to handle
- Why this approach is chosen
```

**7. Request code in iterative chunks**

Don't ask for an entire application at once - break it into small building blocks.

```
ITERATION 1:
Create the basic User model class with properties: id, username, email, created_at

ITERATION 2 (after reviewing iteration 1):
Add methods for password hashing and validation to the User class

ITERATION 3:
Create the UserRepository class that handles database operations for User objects
```

**Common coding prompt patterns:**

```
PATTERN: Specification-First
1. First describe what the code should do (specification)
2. Then generate the code
3. Then generate tests that verify the specification

PATTERN: Example-Driven
Input: [1, 3, 2, 5, 4]
Expected Output: [1, 2, 3, 4, 5]

Input: []
Expected Output: []

Now implement the sorting function

PATTERN: Error-Driven Refinement
Here's code that's failing:
[paste code]

Error message:
[paste error]

Debug this code, explain what's wrong, and provide the corrected version
```

**Production-ready code prompts:**

```
Generate production-ready code for [task]:

Requirements:
- Error handling for all edge cases
- Input validation
- Logging for debugging
- Type hints/type safety
- Performance optimization
- Security considerations (SQL injection, XSS, etc.)
- Comprehensive tests (unit + integration)
- Clear documentation

Provide:
1. Main implementation
2. Test suite
3. Usage examples
4. Performance characteristics
5. Security audit notes
```

---

