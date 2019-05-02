If you're working with microservices, at a sufficiently large organization, or just need some uniformity around the repositories your team creates, a [_project generator_](http://localhost:8000/developer/create/) can help you establish a consistent and reproducible framework for your projects.

A project generator is a type of [command](http://localhost:8000/developer/commands/) which takes a template repository and creates a brand new one from it. It's different from forking in that the generated repository shares no Git history with the original template: it's literally just a copy. These templates repositories are also called _seeds_.

In this example, we'll set up a project generator that takes a Java Spring project with some Maven dependencies configured. (You can generate a project from a seed in any language.)

## Prerequisites

You should already have [a blank SDM](/developer/sdm/#creating-an-sdm-project), though you can also use an existing one if you'd like.

We'll be using the [atomist-seeds/spring-rest](https://github.com/atomist-seeds/spring-rest) repository as our seed. Please don't use this seed for your own Spring projects, as this is just a demo app (and likely full of outdated packages!).

## Registering a generator

Establishing a generator only requires two core arguments: the location of the seed repository, and the name of the command to run to initiate the project.

Open up your SDM, and add a new file called `lib/generator.ts`. Paste the following lines to get started with your generator:

```typescript
import { GitHubRepoRef } from "@atomist/automation-client";
import { GeneratorRegistration } from "@atomist/sdm";
```

The `GitHubRepoRef` function is going to grab a repository off of GitHub based on the parameters you provide it. This could also be in a location like [`GitLabRepoRef`](https://atomist.github.io/automation-client/modules/_lib_operations_common_gitlabreporef_.html) or [`BitBucketRepoRef`](https://atomist.github.io/automation-client/modules/_lib_operations_common_bitbucketreporef_.html) if the repository is hosted somewhere else. In any case, all of the following code will work the same, regardless of the seed's location.

`GeneratorRegistration` is the interface which will actually define the project generation command. Let's fill that out in this file next:

```typescript
export const JavaSpringGenerator: GeneratorRegistration = {
  name: "Java Spring Project",
  intent: "create java-spring project",
  startingPoint: GitHubRepoRef.from({
      owner: "atomist-seeds",
      repo: "spring-rest",
  }),
  transform: [],
};
```

The arguments are somewhat self-explanatory:

* `name` of the generator. This can be any string.
* `intent` a string or array of strings; you'll type this to trigger the command.
* `startingPoint` provides the generator with the location for the seed repository. You can also specify specific SHAs or branches—see [RepoRef](reporef.md) for more details.
* `transform` is an array of zero or more [code transforms](transform.md) to apply. We won't get into that in this tutorial just yet!

## Adding the generator

Next, in your `machine.ts` file, add the following line to import the file you've just created:

```typescript
import {
    JavaSpringGenerator,
} from "./generator";
```

Within the SDM logic itself, you'll add the generator via the `addGeneratorCommand` method:

```typescript
sdm.addGeneratorCommand(JavaSpringGenerator);
```

That's all there is to it!

## Testing the generator

{!tbd.md!}