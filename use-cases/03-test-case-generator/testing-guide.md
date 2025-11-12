# Testing Guide: Test Case Generator

## Quick Testing Protocol

### Test 1: Simple Feature
**Input**: User Login requirement
**Expected**: 10-15 test cases (happy path, negative, boundary)
**Verify**: All standard scenarios covered

### Test 2: Complex Workflow  
**Input**: Multi-step approval process
**Expected**: 20-30 test cases covering all workflow paths
**Verify**: Decision points and exceptions handled

### Test 3: Edge Cases
**Input**: Requirement with complex validation rules
**Expected**: Boundary and edge case test cases generated
**Verify**: Min/max values, special characters, limits tested

### Test 4: Coverage Matrix
**Ask**: "Show coverage matrix for [Feature]"
**Verify**: All scenarios categorized (Happy/Negative/Boundary/Edge)

### Test 5: Test Data Generation
**Ask**: "Generate test data for [Test Case]"
**Verify**: Valid and invalid data sets provided

## Acceptance Criteria

- [ ] Test case generation time < 2 minutes
- [ ] Coverage includes all scenario types
- [ ] Format matches EGAT template
- [ ] Test data is realistic
- [ ] QA team can use without modification (80%)

## Common Issues

**Issue**: Missing scenarios
**Fix**: Improve requirement detail

**Issue**: Wrong format
**Fix**: Update template in knowledge base

**Issue**: Duplicate test cases
**Fix**: Refine agent instructions

---

See [implementation-guide.md](./implementation-guide.md) for full testing procedures.
