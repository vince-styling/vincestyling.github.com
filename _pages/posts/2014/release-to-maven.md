title: release your project into maven repository via Sonatype
decorator: post
description: Walking with me step by step to release our project into maven repository, this's my experience to record, i believe it could be helpful in my future work and more.
identifier: cb0ddc9e5ffb5daee2eb
‡‡‡‡‡‡‡‡‡‡‡‡‡‡

Maven is the popular place where we hosted our java open source projects so we can share with others more simply. Unlike to `RubyGems`, publish to Maven Central repository was a bit of difficult and complication for a newbie, even an experienced programmer. Unfortunately I didn't figure out a complete step-by-step tutorial to help, thus I decide to archive the experience in my blog.

If you did the search for this knowledge ever, you surely aware that the most people knows approach is work with Sonatype, of course I'm follow that way as well.


# Guidance


## Register a JIRA account

To begin, you'll need to [register a JIRA account](https://issues.sonatype.org/secure/Signup!default.jspa), then configure that account on the maven settings file `~/.m2/settings.xml`, add below information in right place :

```xml
<servers>
    <server>
        <id>sonatype-nexus-snapshots</id>
        <username>[Your Username]</username>
        <password>[Your Password]</password>
    </server>
    <server>
        <id>sonatype-nexus-staging</id>
        <username>[Your Username]</username>
        <password>[Your Password]</password>
    </server>
</servers>
```


## Create an Issue of your project

Secondly, [create an Issue of your project](https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134) and fill up the project's information you can find. After that, we start to waiting the `human review`, Sonatype will prepare Maven repositories for us and set the issue status as **Resolved** once it get done. You may receive an e-mail notice indicating that the issue is resolved, please do not deploy until you received it. Normally this step would takes less than 2 business days, if you havent receive that e-mail, you can leave a comment on the JIRA issue consider as remind them. The relate role who assign to handle your issue will update it quickly. If they write back as comment info like following, that means you can start to release your project.

```
Configuration has been prepared, now you can:
...
please comment on this issue when you promoted your first release, thanks
```


### Create an issue does not always necessary

Sometime, in fact, the procedure of "Create an Issue of your project" can be omitted if you've ever done release before. I reckon that the main mission of the Sonatype staff done in `human review` which make us waiting a bit of time is verify the GroupId which you gave whether holding by others or available for you. For instance, if you claim a GroupId as "org.apache" which already belong to Apache using, then you'll be asking to prove the ownership or have to change other's.

In other words, meanwhile you release an artifact successfully, you'll able to own all the necessary permissions of the GroupId you has used, you `needn't` raise any issue for the subsequent artifacts when you going to release by the same GroupId. **Only one JIRE issue per top-level GroupId is necessary**, heard it in recent when I'm going to release my another artifact "asqliteplus" with the same GroupId "com.vincestyling", the [JIRA issue 13140](https://issues.sonatype.org/browse/OSSRH-13140) would describe more.



## Prepare the GPG key

Find way yourself to install `GnuPG` then use it to generate a key pair :

```bash
~ $ gpg --gen-key
```

If it's your first time to generate the key, make yourself never forget the passphrase because it would be required in next time you release any artifacts, i encountered the passphrase lost problem before, it was too bored for get back this, so don't follow my step at this aspect. If you sadly forgot the passphrase, you should delete the past key then re-generate a new key, don't use a duplicate key name at all. e.g. if you ever generated a key named `vincestyling`, then you couldn't use it again, it would be causing a name conflict in the key server. Although you can release successful, but the name conflict will make you failed with some fatal errors like `signature failed` when you try to close and release the Staging Repository in future.

After generated the key pair, we should take the public key and distribute it to the remote server, use `gpg -k` to grab the key we just had :

```bash
~ $ gpg -k

/Users/vince/.gnupg/pubring.gpg
-------------------------------
pub   2048R/F526C615 2014-08-01 [expires: 2014-08-13]
uid                  lingyunxiao (lingyunxiao) <lingyunxiao@qq.com>
sub   2048R/F32F736E 2014-08-01 [expires: 2014-08-13]
```

The output information like above, keep an eye on the `F526C615` string, it's your public key which you would distribute to, copy where the F526C615 place's string then distributing it :

```bash
~ $ gpg --keyserver hkp://pgp.mit.edu --send-keys F526C615
```

To be safely, we need to make sure that just only one key we have in our computer. If more, we must remove the rest to prevent the name conflict problem. You can use command `gpg --delete-keys` or install the Graphic Application "GPG keychain Access" to checking this :

![GPG keychain Access](/images/release_to_maven_keychain.png "GPG keychain Access")

At this time, we finished all the works of GPG KEY. As supplement, you can checking this [post](http://blog.sonatype.com/2010/01/how-to-generate-pgp-signatures-with-maven) for more details.


### Handle the GPG key expire problem

It's still suggest you to have the GPG key expired while generating it even if which will causing the release failed in the next time you attempt to releasing an artifact. You'll got **signing failed: Unusable secret key** error if it expired during preparing the artifact. But it's quite easy to resolve via `extend` the expiration date of that GPG key. You can follow [this post](http://www.g-loaded.eu/2010/11/01/change-expiration-date-gpg-key/) to learn how to do it.



## Configure your project module

In the project module which you intend to publishing, configure some necessary messages included adopt sonatype as module's parent, apply a `Snapshot` version number, configure the `scm` information, configure maven release `plugin` etc.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <!-- Important step : make sonatype as your project's root. -->
    <parent>
        <groupId>org.sonatype.oss</groupId>
        <artifactId>oss-parent</artifactId>
        <relativePath />
        <version>7</version>
    </parent>

    <!-- add the project description. -->
    <groupId>com.vincestyling</groupId>
    <artifactId>traversaless-viewpager</artifactId>
    <!-- here must declare as "-SNAPSHOT" phrase. -->
    <version>4.0.1_r1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <url>https://github.com/vince-styling/traversaless-viewpager-android</url>
    <description>Traversal less ViewPager widget for Android</description>
    <inceptionYear>2014</inceptionYear>

    <licenses>
        <license>
            <name>Apache License Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
            <distribution>repo</distribution>
        </license>
    </licenses>

    <!-- Important step : maven will commit a few version and
            tagging the release version, so don't forget this. -->
    <scm>
        <connection>scm:git:git@github.com:vince-styling/traversaless-viewpager-android.git</connection>
        <developerConnection>scm:git:git@github.com:vince-styling/traversaless-viewpager-android.git</developerConnection>
        <url>git@github.com:vince-styling/traversaless-viewpager-android.git</url>
        <tag>HEAD</tag>
    </scm>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.0</version>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
            </plugin>

            <plugin>
                <groupId>com.jayway.maven.plugins.android.generation2</groupId>
                <artifactId>android-maven-plugin</artifactId>
                <version>3.7.0</version>
                <configuration>
                    <sdk>
                        <platform>14</platform>
                    </sdk>
                </configuration>
            </plugin>

            <!-- Important step : config the maven release plugin. -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <version>2.4.2</version>
                <configuration>
                    <goals>deploy</goals>
                    <autoVersionSubmodules>true</autoVersionSubmodules>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### release a snapshot version

Now we are going to release a snapshot version to the `Staging Repository`, open your Terminal and point to following commands :

```bash
~ $ mvn release:clean
~ $ mvn release:prepare
~ $ mvn release:perform
```

In the `prepare` goal, Maven would ask you the `GPG passphrase` several times, also tagging your current version, commit two changes, push all modifications to the `remotes` at last. Because Maven did a few VCS operations, thus we should rollback them if we got something wrong with this release process.

```bash
`10a55a3b5b9b4d473e78ccd0c60af0672d7adecd` is commit hash code before release.
~ $ git reset --hard 10a55a3b5b9b4d473e78ccd0c60af0672d7adecd
~ $ git tag -d TAG_NAME
~ $ git push origin :refs/tags/TAG_NAME
~ $ git push --force
```

Then perform the "release a snapshot version" operation again.



## Release it in the Sonatype

After above release was done successfully, we are ready to do the next job in Sonatype to release the artifact. Open your favorite browser and navigate to [https://oss.sonatype.org/](https://oss.sonatype.org/). You should `Log In` by the earlier account, then go to the `Build Promotion` tab's `Staging Repositories` which in the left pane, find out the Repository that you just release(normally at the list bottom), click the "Close" button then do the rest.

![Release It](/images/release_to_maven_release_it.png "Release It")

Sometime you'll get the error like 'signature failed', remind you to solve the GPG Key pair conflict problem. If happens, you should drop the artifact, then re-release again. Ensure the "Activity" field reported **Last operation completed successfully** before you move on.

![Release It](/images/release_to_maven_release_it_errors.png "Release It")

If you didn't see any errors in the bottom "Activity" pane, means the `Release` button might already be enable, keep Refresh the Repositories until you got it, now it's time to do the final step :

![Release It](/images/release_to_maven_release_it_final.png "Release It")

Congratulation, you've done all jobs about release, once you get successfully, your artifact will be synchronize to the Maven Central soon enough. The best way to verify whether success or not is search your artifact in [search.maven.org](http://search.maven.org/), but it may take time before your artifact display, typically within 10 minutes even hours, so be patient with it.



# Final Thoughts

Throughout this improvement of my ability process, i referenced many articles, it would be detailedly to mark them in the end. I followed this [guide](http://central.sonatype.org/pages/ossrh-guide.html) and [docs](https://docs.sonatype.org/pages/diffpages.action?pageId=6619209&originalId=13598740) in Sonatype all the time. Also below are two release issues of mine to record.

- [OSSRH-9739](https://issues.sonatype.org/browse/OSSRH-9739)
- [OSSRH-10903](https://issues.sonatype.org/browse/OSSRH-10903)








