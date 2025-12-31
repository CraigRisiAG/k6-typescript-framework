# Dependency Upgrade Notes - December 31, 2025

This document provides detailed information about the dependency upgrades performed and any migration steps needed.

## Summary

All project dependencies and devDependencies have been upgraded to their latest stable versions. The project builds and type-checks successfully with no errors.

## Dependency Changes

### Runtime Dependencies

| Package | Old Version | New Version | Change |
|---------|------------|-------------|--------|
| @types/k6 | 0.25.1 | 1.4.0 | Major |
| @types/node | 13.7.1 | 25.0.3 | Major |

### Development Dependencies

| Package | Old Version | New Version | Change |
|---------|------------|-------------|--------|
| @babel/cli | 7.8.4 | 7.28.3 | Minor |
| @babel/core | 7.8.4 | 7.28.5 | Minor |
| @babel/node | 7.8.4 | 7.28.0 | Minor |
| @babel/preset-env | 7.8.4 | 7.28.5 | Minor |
| @babel/preset-typescript | 7.8.3 | 7.28.5 | Minor |
| babel-loader | 8.1.0 | 10.0.0 | Major |
| eslint | 6.8.0 | 9.39.2 | Major |
| eslint-config-prettier | 6.10.0 | 10.1.8 | Major |
| eslint-plugin-prettier | 3.1.2 | 5.5.4 | Major |
| prettier | 1.19.1 | 3.7.4 | Major |
| typescript | 4.7.0 | 5.9.3 | Major |
| webpack | 5.74.0 | 5.104.1 | Patch |
| webpack-cli | 4.10.0 | 6.0.1 | Major |
| typescript-eslint | (new) | 8.51.0 | New |

### Babel Plugin Migrations

| Old Package | New Package | Version |
|-------------|-------------|---------|
| @babel/plugin-proposal-class-properties | @babel/plugin-transform-class-properties | 7.27.1 |
| @babel/plugin-proposal-object-rest-spread | @babel/plugin-transform-object-rest-spread | 7.28.4 |

**Reason**: The proposal plugins were deprecated because these features are now part of the ECMAScript standard. The transform plugins provide the same functionality.

## Configuration Changes

### 1. Babel Configuration (`.babelrc` and `webpack.config.js`)

**What Changed**: 
- Replaced `@babel/proposal-class-properties` with `@babel/plugin-transform-class-properties`
- Replaced `@babel/proposal-object-rest-spread` with `@babel/plugin-transform-object-rest-spread`

**Why**: The proposal plugins have been deprecated since these features are now part of the ES standard.

**Impact**: No functional changes; builds continue to work identically.

### 2. ESLint Configuration

**What Changed**: 
- Created new `eslint.config.js` with ESLint 9 flat config format
- Added `typescript-eslint` package for TypeScript support
- Integrated Prettier for code formatting
- Added lint scripts to package.json: `yarn lint` and `yarn lint:fix`

**Why**: ESLint 9 requires the new flat config format and no longer supports legacy `.eslintrc.*` files.

**Migration Steps**:
1. The new `eslint.config.js` has been created automatically
2. If you previously had a `.eslintrc.*` file (there wasn't one), it would need to be removed
3. Run `yarn lint` to check for issues
4. Run `yarn lint:fix` to auto-fix formatting issues

**Known Issues**:
- ESLint reports 18 warnings about unused variables and `any` types in existing code
- These are non-blocking warnings that can be addressed incrementally
- Variables with names starting with `_` are intentionally ignored by the linter

### 3. Prettier Configuration

**What Changed**: 
- Created `.prettierrc` with standard formatting rules
- All TypeScript source files have been auto-formatted

**Why**: Prettier 3 requires explicit configuration for consistent formatting.

**Impact**: 
- Minor whitespace and quote style changes in source files
- No functional changes to code
- Consistent formatting across the codebase

### 4. TypeScript

**What Changed**: 
- Upgraded from TypeScript 4.7 to 5.9.3

**Why**: TypeScript 5 includes improved type inference and performance

**Impact**: 
- All existing code continues to type-check successfully
- Future code may encounter stricter type checking
- No changes required to `tsconfig.json`

## Breaking Changes

### ESLint 9

**Breaking**: ESLint 9 requires flat config format (`eslint.config.js`)

**Migration**: 
- New config file created in this PR
- If you had custom ESLint rules, they need to be migrated to the flat config format
- See: https://eslint.org/docs/latest/use/configure/migration-guide

### Babel Plugins

**Breaking**: Proposal plugins renamed to transform plugins

**Migration**: 
- Configuration files have been updated automatically
- If you have Babel config elsewhere (e.g., in other package.json scripts), update plugin names

### Prettier 3

**Breaking**: Updated formatting rules

**Migration**:
- All files have been auto-formatted in this PR
- New code will automatically follow Prettier 3 rules
- If you have IDE integrations, ensure they use the project's Prettier version

### TypeScript 5

**Breaking**: Stricter type checking in some scenarios

**Migration**:
- All current code type-checks successfully
- Be aware of stricter checks when writing new code
- If you encounter new type errors, they are likely legitimate issues

## Verification

All checks pass successfully:

```bash
✅ yarn check-types  # TypeScript compilation - 0 errors
✅ yarn build        # Webpack build - success
✅ yarn lint         # ESLint - 0 errors, 18 warnings
✅ Security scan     # No vulnerabilities found
✅ CodeQL            # 0 security alerts
```

## Testing Recommendations

After pulling this branch:

1. **Install dependencies**: `yarn install`
2. **Verify build**: `yarn build`
3. **Check types**: `yarn check-types`
4. **Run linter**: `yarn lint`
5. **Test with Docker**: `yarn go:docker` (if you have Docker)
6. **Run k6 locally**: `yarn go:k6` (if you have k6 installed)

## ESLint Warnings

The following warnings are present in the codebase and are not related to the upgrade:

- **Unused variables**: Some variables are defined but never used (e.g., in imports)
- **`any` types**: Some functions use `any` types for flexibility
- **Empty object types**: Some functions use `{}` type
- **`require()` imports**: One file uses `require()` instead of ES6 imports

These warnings do not block the build and can be addressed incrementally as needed.

## Security

- **No vulnerabilities found** in any of the updated dependencies (verified with gh-advisory-database)
- **No CodeQL alerts** detected in the codebase
- All dependencies are at their latest stable versions as of December 31, 2025

## Rollback Instructions

If you need to rollback these changes:

```bash
git revert <commit-hash>
yarn install
yarn build
```

Or restore the previous `package.json` and `yarn.lock` from the main branch.

## Future Considerations

1. **Address ESLint warnings**: Review and fix unused imports and variables
2. **Type improvements**: Replace `any` types with more specific types where possible
3. **ES6 imports**: Convert `require()` to ES6 import statements
4. **Dependency updates**: Consider setting up automated dependency updates with Dependabot or Renovate

## Support

If you encounter any issues with the upgraded dependencies:

1. Check this document for migration steps
2. Review the ESLint 9 migration guide: https://eslint.org/docs/latest/use/configure/migration-guide
3. Review the TypeScript 5 release notes: https://devblogs.microsoft.com/typescript/
4. Review the Prettier 3 release notes: https://prettier.io/blog/2023/07/05/3.0.0.html

## Questions or Issues?

If you have questions or encounter issues:
- Check the verification commands above
- Review the package.json for exact versions
- Open an issue on the repository with details about the problem
