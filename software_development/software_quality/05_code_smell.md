## Code Smells (9 out of 24)

- Duplicated Code
- Long Method
- Global Data
- Mutable Data
- Shotgun Surgery
- Feature Envy
- Data Clump
- Primitive Obsession
- Large Class

### Long Method

- Problem: Breaks SRP
- Solution: Extract method

### Long Class

- Problem: Breaks SRP
- Solution: Extract class

### Duplicated Code

- Problem: Changing one feature requires multiple changes in many parts of app
- Solution: Extract class / method

### Global Data

- Problem: Accessible anywhere, leads to unpredictable changes
- Solution: Encapsulate variable / collection

### Mutable Data

- Problem: Changes from outside leads to unpredictable behavior
- Solution: Encapsulate variable / collection

### Shotgun Surgery

- Problem: Changing one feature requires multiple changes in many parts of app
- Solution: Extract class

### Feature Envy

- Problem: Method more interested in other class
- Solution: Move method

### Primitive Obsession

- Problem: Overuse of primitive type
- Solution:  Replace primitive with object

### Data Clumps

- Problem: Group of variables that are mostly used together
- Solution: Introduce parameter object
