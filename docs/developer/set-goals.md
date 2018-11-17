This page assumes you have [created some goals][create-goals]. It shows how to:

*  group goals
*  set dependencies between goals
*  choose which goals to execute on each push
*  prevent the SDM from setting goals on a push

[create-goals]: goal.md (Goals and how to create them)

## Grouping goals

You can group goals into sets. Here, two goals are grouped: code inspection (but no code inspections are registered) and the Autofix goal.

```typescript
    const BaseGoals = goals("checks")
        .plan(new AutoCodeInspection())
        .plan(autofix);
```

## Dependencies

You can specify ordering, if some goals should wait for others to succeed. Here, we don't want to start the build until after Autofixes have completed.
If the autofixes do anything, they'll make a new commit, and we don't bother building this one.

```typescript
    const BuildGoals = goals("build")
        .plan(new Build().with({ builder: mavenBuilder() }))
        .after(autofix);
```

## Set goals on push

Finally, you can tell the SDM which goals to run on each push. Here, we set the BaseGoals (inspection and autofix) on every push. Then if 
this is a Maven project (identified by having a pom.xml), we do the build as well.

```typescript
    sdm.withPushRules(
        onAnyPush().setGoals(BaseGoals),
        whenPushSatisfies(IsMaven).setGoals(BuildGoals),
    );
```

## PushRule

Each argument to `[sdm.withPushRules](https://atomist.github.io/sdm/interfaces/_lib_api_machine_softwaredeliverymachine_.softwaredeliverymachine.html#withpushrules)`
is a PushRule, contributing goals on a commit if a condition is met. That condition is a PushTest.

## PushTest

Push tests are functions that look at the content of a push and decide whether this goal applies.

See: [Documentation on Push Tests](push-test.md)

Here's a quick example of a push test:

```typescript
export const IsMaven: PredicatePushTest = predicatePushTest(
    "Is Maven",
    p => p.hasFile("pom.xml"));
```

To establish a PushTest for whether a project uses Maven as a build tool, 
[this code](https://github.com/atomist/sdm-pack-spring/blob/3fcadc309231e45fa25a8ccde0cf25587ade6d71/lib/maven/pushtest/pushTests.ts#L33)
calls a constructor function
[predicatePushTest](https://atomist.github.io/sdm/modules/_lib_api_mapping_pushtest_.html#predicatepushtest-1) 
with a name for the PushTest and a function from a Project to a `Promise<Boolean>`.

The example [spring-sdm](https://github.com/atomist-seeds/spring-sdm/blob/1ab4ab06086e61f0e3395b1b7114a91a59d8939d/lib/machine/machine.ts#L84) uses this PushTest to create a PushRule, which sets build goals only on Maven projects:

```typescript
whenPushSatisfies(IsMaven).setGoals(buildGoals)
```

## Preconditions

{!tbd.md!}

## Stop setting goals

{!tbd.md!}
