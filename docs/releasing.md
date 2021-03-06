# uPortal-app-framework release engineering

Release engineering for `uPortal-app-framework` is pretty automated. We actually only have to do a few things.

## Release authorization

This project has [adopted Apache rules][] with necessary or pragmatic local adaptations.

Release authorization, or the delegation of release authority to one or more release engineers, should be documented by [a suitable vote][Apache Release Policy re Release Approval] of [the uPortal-app-framework Committers][] on [uportal-dev@][].

## Select a version

### Labeling pull requests

Look at the [closed pull requests][uportal-app-framework closed pull requests] that were done since the last release. There should be labels on pull requests communicating what kind of release it's appropriate to include them in: major, minor, or patch (x.y.z respectively). (See [Semantic Versioning][], especially if you're adding missing change-type labels on those closed pull requests.)

### Determining the release version from those labels

The next release should be the least-significant kind of release appropriate to include all the changes. For example, if it's all patch-appropriate change, you're cutting a patch release, but if there's even just one breaking change, it's a major release.

Now that you have a version you need to update that in a few places.

### Milestone in GitHub

+ [Create a milestone][uportal-app-framework milestones] named for the version you're cutting, if it does not yet exist.
+ If using an existing milestone, ensure nothing is currently erroneously marked with that milestone, e.g. open pull requests or issues that someone hoped might be resolved for this version but alas were not. You can examine this by simply [clicking the milestone's name][uportal-app-framework milestones] to view the milestone.
+ Mark the [pull requests that merged to `master`][uportal-app-framework closed pull requests] since the previous release with that milestone.
+ [Close the milestone][uportal-app-framework milestones].

Tada! The pull requests in this release now say the version they released in right on them. And there's a handy milestone that rolls up all the pull requests in this version.

## Release steps

### Finalize documentation

Before cutting the release, do what you can to square away the documentation for the release. Add the pull requests from the milestone to the [CHANGELOG][].

### Java release

Now do release for Java.

```sh
mvn release:prepare
mvn release:perform
```

_Iff it went well_

```sh
git push origin master
git push origin uportal-app-framework-maven-<version>
```

+ Release maven artifacts from [central staging repository][central Maven staging repository]


### Node release

+ Update the npm package by running `npm version <update_type>` where <update_type> is based on the selection above: patch, minor, or major. This will: change the version in `package.json`, commit that (locally), and make a tag in the format `vx.y.z`. [Read more about publishing on npm docs](https://docs.npmjs.com/getting-started/publishing-npm-packages#updating-the-package).
+ Deploy npm package to the world; `npm publish`. (if you don't have [publish rights][uportal-app-framework access on npm] contact authors)
+ Push git changes to the github via `git push origin master` and `git push origin <tag>`

### Docker release

Deploy a new version to `docker.doit.wisc.edu` (note the version below x.y.z should be the release version)

```
git checkout vx.y.z
docker build -t docker.doit.wisc.edu/myuw/uportal-app-framework-superstatic:x.y.z .
docker push docker.doit.wisc.edu/myuw/uportal-app-framework-superstatic:x.y.z
```

### Release notes

* There should now be a couple tags above the `Latest release` on [the GitHub releases page][uportal-app-framework releases]
* Click into the top one ( `uportal-app-framework-maven-x.y.z`)
* Edit tag
* Title the release `x.y.z`, optionally `x.y.z - pithy summary`
* Add release notes, linking pull requests included in this release, linking the newly released documentation, and especially articulating anything interesting about the upgrade path to this release for frame-based app developers.

### Update My App Seed

[My App Seed][my-app-seed] is a template for developers to use when creating new frame apps. When cutting a release, be sure to update the [my-app-seed pom][] so future adopters can start developing using the latest code.

### Updating JIRA issues

UW-Madison uses a private JIRA instance tracking work on this project. If the
Release Engineer does not have access to the instance, enlist a
[MyUW Developer][the uPortal-app-framework Committers] on [uportal-dev@][] to
perform this.

In the `MUMUP` JIRA project, find the last unreleased patch version. If the
current release is a major or minor update, rename the version accordingly.
Apply that version as a `Fix Version` to the JIRA tickets resolved since the
last release. When all tickets have been added, `Release` the version and
create the next patch version.

### Communicating

* Announce the release on the [MyUW Developer Group][]


[Semantic Versioning]: http://semver.org/
[uportal-app-framework closed pull requests]: https://github.com/uPortal-Project/uportal-app-framework/pulls?q=is%3Apr+is%3Aclosed
[uportal-app-framework milestones]: https://github.com/uPortal-Project/uportal-app-framework/milestones
[CHANGELOG]: ../CHANGELOG.md
[uportal-app-framework access on npm]: https://www.npmjs.com/package/@uportal/app-framework/access
[central Maven staging repository]: https://oss.sonatype.org/#stagingRepositories
[uportal-app-framework releases]: https://github.com/uPortal-Project/uportal-app-framework/releases
[MyUW Developer Group]: https://groups.google.com/forum/#!forum/myuw-developers
[adopted Apache rules]: https://github.com/uPortal-Project/uportal-app-framework/blob/master/committers.md#rules
[Apache Release Policy re Release Approval]: http://www.apache.org/legal/release-policy.html#release-approval
[the uPortal-app-framework Committers]: https://github.com/uPortal-Project/uportal-app-framework/blob/master/committers.md#who-are-the-committers
[uportal-dev@]: https://groups.google.com/a/apereo.org/forum/#!forum/uportal-dev
[my-app-seed]: https://github.com/UW-Madison-DoIT/my-app-seed
[my-app-seed pom]: https://github.com/UW-Madison-DoIT/my-app-seed/blob/master/pom.xml
