# AST-Grep (sg) Quick Reference

## When to Use
Use `sg` for syntax-aware code search instead of grep/ripgrep when you need:
- Structural pattern matching (functions, classes, imports)
- Code refactoring across files
- Semantic search regardless of formatting

## Basic Patterns
```bash
# Function calls
sg --pattern 'myFunction($$$)' --lang typescript

# Variable declarations  
sg --pattern 'const $VAR = $$$' --lang typescript

# Method calls
sg --pattern '$OBJ.methodName($$$)' --lang typescript

# Class declarations
sg --pattern 'class $NAME { $$$ }' --lang typescript

# Imports from specific module
sg --pattern 'import { $$$ } from "$MODULE"' --lang typescript

# React hooks
sg --pattern 'const [$STATE, $SETTER] = useState($$$)' --lang typescript

# Interface/type definitions
sg --pattern 'interface $NAME { $$$ }' --lang typescript

# Async functions
sg --pattern 'async function $NAME($$$) { $$$ }' --lang typescript

# Generic functions
sg --pattern 'function $NAME<$T>($$$): $T { $$$ }' --lang typescript
```

## Pattern Variables
- `$VAR` - Single AST node (variable, expression)
- `$$$` - Multiple nodes (zero or more)
- `$$` - Single statement/expression

## Useful Flags
```bash
--lang typescript          # Specify language
--paths src/               # Search specific directory
--files-with-matches       # Show only filenames
-A 3 -B 3                 # Show context lines
--rewrite 'new code'       # Replace matches
```

## Common Use Cases
```bash
# Find all TODO comments
sg --pattern '// TODO: $$$' --lang typescript

# Find service dependencies
sg --pattern 'constructor($$$ private $SERVICE: $TYPE $$$)' --lang typescript

# Find HTTP endpoints
sg --pattern '@$HTTP("$PATH")' --lang typescript

# Refactor console.log to logger
sg --pattern 'console.log($MSG)' --rewrite 'logger.info($MSG)' --lang typescript
```

## Fallback: Ripgrep (rg)
When `sg` isn't available or suitable, use ripgrep for fast text search:

```bash
# Basic search
rg "pattern" 

# Search specific file types
rg "pattern" --type ts

# Search with context
rg "pattern" -A 3 -B 3

# Case insensitive
rg "pattern" -i

# Show only filenames
rg "pattern" -l
```

## Comby Alternative
Comby is another structural search/replace tool with simpler syntax:

```bash
# Basic pattern (:[var] matches expressions)
comby 'console.log(:[msg])' 'logger.info(:[msg])' .ts

# Match anything with :[hole] 
comby 'function :[name](:[args]) { :[body] }' 'const :[name] = (:[args]) => { :[body] }' .ts

# Multiple holes
comby 'if (:[cond]) return :[val]' 'return :[cond] ? :[val] : undefined' .ts

# Preview changes
comby 'old' 'new' .ts -review

# Apply changes
comby 'old' 'new' .ts -in-place
```

**When to use Comby vs ast-grep:**
- **Comby**: Simpler syntax, better for straightforward refactoring
- **ast-grep**: More precise AST matching, better for complex patterns

## Pro Tips
- Always specify `--lang` for accurate parsing with sg
- Test patterns on small files first  
- Combine with other tools: `sg ... | grep -v test`