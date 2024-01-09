# What is this?

This is a minimal, reproducible example of a bug in the [maven-sources-plugin][1] which is tracked by several JIRA issues:

- [MSOURCES-140][2]
- [MSOURCES-141][3]
- [MSOURCES-143][4]

Basically if your build fails with the following message:

> [ERROR] Failed to execute goal org.apache.maven.plugins:maven-source-plugin:3.3.0:jar-no-fork (attach-sources) on project source-plugin-breaks-releases: Presumably you have configured maven-source-plugn to execute twice times in your build. You have to configure a classifier for at least on of them. -> [Help 1]

Then you have hit some variant of this bug.

This repository particularly focuses on reproducing the bug when trying to perform a Maven release.

# How do I reproduce the bug from this repository?

Just clone the code then try the following:

```bash
$ mvn release:perform
```

# What causes this bug?

This particular instance of the bug is caused be the interaction of two things:

1. The new duplicate invocation check within the `maven-source-plugin` that was added as of their `3.3.0` release
2. The `maven-release-plugin`'s usage of the [implicit release profile][5] that includes an extra invocation of the
   `maven-source-plugin` that leads to the duplicate invocation check failing

# Is there a workaround?

There are some known workarounds at this time:

1. Rollback to using `maven-source-plugin` versions prior to `3.3.0` e.g. `3.2.1` was the last release without the
   duplicate invocation check present
2. Explicitly use a `maven-release-plugin` release that is `3.0.0` or higher.  As of their 3.x releases they don't enable the [implicit release profile][5] by default so you won't get the extra `maven-source-plugin` invocation added and hit the bug.  You can do this as follows in the `<pluginManagement>` section of your `pom.xml` as follows:





[1]: https://maven.apache.org/plugins/maven-source-plugin/
[2]: https://issues.apache.org/jira/browse/MSOURCES-140
[3]: https://issues.apache.org/jira/browse/MSOURCES-141
[4]: https://issues.apache.org/jira/browse/MSOURCES-143
[5]: https://maven.apache.org/maven-release/maven-release-plugin/perform-mojo.html#usereleaseprofile
