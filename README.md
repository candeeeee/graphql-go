# graphql-go

[![Sourcegraph](https://sourcegraph.com/github.com/qdentity/graphql-go/-/badge.svg)](https://sourcegraph.com/github.com/qdentity/graphql-go?badge)
[![Build Status](https://travis-ci.org/qdentity/graphql-go.svg?branch=master)](https://travis-ci.org/qdentity/graphql-go)
[![GoDoc](https://godoc.org/github.com/qdentity/graphql-go?status.svg)](https://godoc.org/github.com/qdentity/graphql-go)

## Changes in this fork
* Support exposing the selected fields in a resolver, commit [5472fce1](https://github.com/qdentity/graphql-go/commit/5472fce1344b4de8067df6cf53d09384c6533ff3), see [graph-gophers/graphql-go#169](https://github.com/graph-gophers/graphql-go/pull/169)
* Replace fmt.Errorf and errors.New with pkg/errors, commit [0c4918ae](https://github.com/qdentity/graphql-go/commit/0c4918aea07404c1e1e16f1ddd899f3d09bd4b29), see [graph-gophers/graphql-go#189](https://github.com/graph-gophers/graphql-go/pull/189)

## Status

### Update: March 1, 2018

This project has transferred ownership from `github.com/neelance/graphql-go` to
`github.com/qdentity/graphql-go`, which will enable us to maintain and evolve the
library [@neelance](https://github.com/neelance) created, and let him focus on WebAssembly / Go development.

I'm super grateful to @neelance for all his work blazing the trail
with `graphql-go`, and I'm ecstatic to work with the community to continue
improving this library.

I'm going to help address issues, review and merge changes, and continue development with
the same ethos as we started.

The immediate goal remains—full support of the GraphQL specification.

- Tony (@tonyghita)

The project is under heavy development. It is stable enough so we use it in production at [Sourcegraph](https://sourcegraph.com), but expect changes.

## Goals

* [ ] full support of [GraphQL spec (October 2016)](https://facebook.github.io/graphql/)
  * [ ] propagation of `null` on resolver errors
  * [x] everything else
* [x] minimal API
* [x] support for context.Context and OpenTracing
* [x] early error detection at application startup by type-checking if the given resolver matches the schema
* [x] resolvers are purely based on method sets (e.g. it's up to you if you want to resolve a GraphQL interface with a Go interface or a Go struct)
* [ ] nice error messages (no internal panics, even with an invalid schema or resolver; please file a bug if you see an internal panic)
  * [x] nice errors on resolver validation
  * [ ] nice errors on all invalid schemas
  * [ ] nice errors on all invalid queries
* [x] panic handling (a panic in a resolver should not take down the whole app)
* [x] parallel execution of resolvers

## (Some) Documentation

### Resolvers

A resolver must have one method for each field of the GraphQL type it resolves. The method name has to be [exported](https://golang.org/ref/spec#Exported_identifiers) and match the field's name in a non-case-sensitive way.

The method has up to two arguments:

- Optional `context.Context` argument.
- Mandatory `*struct { ... }` argument if the corresponding GraphQL field has arguments. The names of the struct fields have to be [exported](https://golang.org/ref/spec#Exported_identifiers) and have to match the names of the GraphQL arguments in a non-case-sensitive way.
- Optional `[]query.SelectedField` argument to receive the tree of selected subfields in the GraphQL query (useful for preloading of database relations)

The method has up to two results:

- The GraphQL field's value as determined by the resolver.
- Optional `error` result.

Example for a simple resolver method:

```go
func (r *helloWorldResolver) Hello() string {
	return "Hello world!"
}
```

The following signature is also allowed:

```go
func (r *helloWorldResolver) Hello(ctx context.Context) (string, error) {
	return "Hello world!", nil
}
```

### Community Examples

[tonyghita/graphql-go-example](https://github.com/tonyghita/graphql-go-example)

[deltaskelta/graphql-go-pets-example](https://github.com/deltaskelta/graphql-go-pets-example) - graphql-go resolving against a sqlite database

[OscarYuen/go-graphql-starter](https://github.com/OscarYuen/go-graphql-starter) - a starter application integrated with dataloader, psql and basic authenication
