# @rsql/ast

Abstract Syntax Tree definitions and functions for RSQL

[![npm](https://img.shields.io/npm/v/@rsql/ast)](https://www.npmjs.com/package/@rsql/ast)
[![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)
[![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg)](https://github.com/prettier/prettier)
[![commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
[![tested with jest](https://img.shields.io/badge/tested_with-jest-99424f.svg)](https://github.com/facebook/jest)
[![auto release](https://img.shields.io/badge/release-auto.svg?colorA=888888&colorB=9B065A&label=auto)](https://github.com/intuit/auto)

## Installation

```sh
# with npm
npm install --save @rsql/ast

# with yarn
yarn add @rsql/ast
```

## API

#### `create<Node>(...args): Node`

```typescript
function createSelectorNode(selector): SelectorNode;
function createValueNode(value): ValueNode;
function createComparisonNode(selector, operator, value): ComparisonNode;
function createLogicNode(left, operator, right): LogicNode;
```

Creates new `Node`, can throw `InvalidArgumentError` for invalid arguments

#### `is<Node>(candidate): boolean`

```typescript
function isNode(candidate): boolean;
function isSelectorNode(candidate): boolean;
function isValueNode(candidate): boolean;
function isComparisonNode(candidate, operator?): boolean;
function isLogicNode(candidate, operator?): boolean;
function isExpressionNode(candidate): boolean;
```

Checks if `candidate` is an instance of `Node`
(actually it doesn't use `instanceof` operator as Nodes are simple objects - not instances of a class).
Additionally we can pass `operator` argument to the `isComparisonNode`
and `isLogicNode` to check if node uses given operator. It automatically maps operators to canonical
versions so for example `isComparisonNode(node, '=le=')` is same as `isComparisonNode(node, '<=')`.

#### `getSelector(comparison): string`

Returns selector for a given `ComparisonNode`.
Can throw `InvalidArgumentError` if passed argument is not a `ComparisonNode`.

#### `getValue(comparison): string | string[]`

Returns value for a given `ComparisonNode`.
Can throw `InvalidArgumentError` if passed argument is not a `ComparisonNode`.

#### `getSingleValue(comparison): string`

Returns single value for a given `ComparisonNode`.
Can throw `InvalidArgumentError` if passed argument is not a `ComparisonNode` or
contains array instead of string.

#### `getMultiValue(comparison): string[]`

Returns multi value for a given `ComparisonNode`.
Can throw `InvalidArgumentError` if passed argument is not a `ComparisonNode` or
contains string instead of array.

#### `ComparisonOperator`

```typescript
const EQ = "==";
const NEQ = "!=";
const LE = "<=";
const GE = ">=";
const LT = "<";
const GT = ">";
const IN = "=in=";
const OUT = "=out=";
const LE_VERBOSE = "=le=";
const GE_VERBOSE = "=ge=";
const LT_VERBOSE = "=lt=";
const GT_VERBOSE = "=gt=";

const CanonicalComparisonOperators = [EQ, NEQ, LE, GE, LT, GT, IN, OUT];
const VerboseComparisonOperators = [LE_VERBOSE, GE_VERBOSE, LT_VERBOSE, GT_VERBOSE];
const ComparisonOperators = [...CanonicalComparisonOperators, ...VerboseComparisonOperators];

function isComparisonOperator(candidate, operator?): boolean;
```

Defines built-in comparison operators and `isComparisonOperator` function which checks if given candidate
is a valid `ComparisonOperator`. Additionally you can pass `operator` argument which checks if
given candidate equals operator from a semantic perspective - so for example
`isComparisonOperator(candidate, LE)` will give you the same result as `isComparisonOperator(candidate, LE_VERBOSE)`

#### `LogicOperator`

```typescript
const AND = ";";
const OR = ",";
const AND_VERBOSE = "and";
const OR_VERBOSE = "or";

const CanonicalLogicOperators = [AND, OR];
const VerboseLogicOperators = [AND_VERBOSE, OR_VERBOSE];
const LogicOperators = [...CanonicalLogicOperators, ...VerboseLogicOperators];

function isLogicOperator(candidate, operator?): boolean;
```

Defines logic operators and `isLogicOperator` function which checks if given candidate
is a valid `LogicOperator`. Additionally you can pass `operator` argument which checks if
given candidate equals operator from a semantic perspective - so for example
`isLogicOperator(candidate, AND)` will give you the same result as `isLogicOperator(candidate, AND_VERBOSE)`

#### `ReservedChars`

```typescript
const ReservedChars = ['"', "'", "(", ")", ";", ",", "=", "!", "~", "<", ">", " ", "\n", "\t", "\r"];
```

Defines list of chars that are reserved in the RSQL.
Used internally to validate selectors, parse RSQL, and escape values.

## Types

#### `Node`

It's a base type for all node types. Defines an object which contains `type` property which defines
type of the node (instead of basing on the `instanceof` operator).

#### `SelectorNode`

Node which represents selector. Defines an object which, besides `Node` properties, contains
a `selector` property which stores selector.

#### `ValueNode`

Node which represents value. Defines an object which, besides `Node` properties, contains
a `value` property which stores value.

#### `BinaryNode`

It's a base type for nodes which consists of two operands and one operator.
Defines an object which, besides `Node` properties, contains `left` and `right` property which
stores operands and `operator` property which stores an operator.

#### `ComparisonNode`

Node which represents comparison expression. It's a constrained version of the `BinaryNode` where
`left` has to be `SelectorNode`, `right` has to be a `ValueNode` and `operator` has to be a
`ComparisonOperator`.

#### `LogicNode`

Node which represents logic expression. It's a constrained version of the `BinaryNode` where
`left` and `right` has to be an `ExpressionNode` and `operator` has to be a `LogicOperator`.

#### `ExpressionNode`

It's a type union between `ComparisonNode` and `LogicNode`.

#### `ComparisonOperator`

It's a string literal type which defines built-in comparison operators.

#### `LogicOperator`

It's a string literal type which defines built-in logic operators.

## License

MIT
