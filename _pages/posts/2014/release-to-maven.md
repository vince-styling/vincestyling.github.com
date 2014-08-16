title: release your project into maven repository via Sonatype
decorator: post
description: Many time I tried to release my project into the Maven Central Repository, now I want to archive some experience in my blog.
identifier: cb0ddc9e5ffb5daee2eb
‡‡‡‡‡‡‡‡‡‡‡‡‡‡

Many time I tried to release my project into the Maven Central Repository, now I want to archive some experience in my blog.

first, you will need to have a JIRA account : [Create your JIRA account](https://issues.sonatype.org/secure/Signup!default.jspa).

configure your JIRA account into the maven settings file `~/.m2/settings.xml`, add below information in right place :

```xml
<servers>
    <server>
        <id>sonatype-nexus-snapshots</id>
        <username>Your Username</username>
        <password>Your Password</password>
    </server>
    <server>
        <id>sonatype-nexus-staging</id>
        <username>Your Username</username>
        <password>Your Password</password>
    </server>
</servers>
```

[Create a New Project Ticket(Issue)](https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134),
fill out the detail information about your project, After the ticket is created, we start to waiting the **human review**,
Sonatype will prepare Maven repositories for you then update the ticket and set it as "Resolved" once it's done.
you shall receive an e-mail notice indicating that the issue is Resolved, please do not deploy until you received it.

Normally it takes less than 2 business days, If you don't receive that e-mail, you can leave a comment on the JIRA ticket with the username,
the relate roles who assign to handle your issue will update the ticket, and if they write back as comment info like this :

```
Configuration has been prepared, now you can:
...
please comment on this ticket when you promoted your first release, thanks
```

that means you can start to release your project.

## Prepare the GPG key :

Install `GnuPG` and generate a key pair :

```bash
$ gpg --gen-key
```

If that was you first time to generate the key, remember your passphrase because it would reuse. but the key can be expire,
if it does or you forgot the passphrase, you should delete the past key then re-generate a new key, but don't use a same name,
i.e if last you generate the key names `vincestyling`, then you can't use it again, this will cause a name conflict in the key server,
although you can release success but the name conflict will cause `signature failed` when you try to close the Staging Repository.

After generated the key pair, we should take the public key and distribute it to the remote server,
use `gpg -k` to show the key we just generate it :

```shell
$ gpg -k

/Users/vince/.gnupg/pubring.gpg
-------------------------------
pub   2048R/F526C615 2014-08-01 [expires: 2014-08-13]
uid                  lingyunxiao (lingyunxiao) <lingyunxiao@qq.com>
sub   2048R/F32F736E 2014-08-01 [expires: 2014-08-13]
```

the output information like this, keep an eye of the `F526C615` string,
it's your public key which you would distribute to,
copy the `F526C615` place's string and send the key :

```shell
$ gpg --keyserver hkp://pgp.mit.edu --send-keys F526C615
```

now, we need to going to make sure that we just only one key store in our computer, and if more,
we must remove it to suppress the name conflict problem, there are two way to do this action,
first you can use command `gpg --delete-keys`, the other way is install the Graphic Application "GPG keychain Access" to do it :

![GPG keychain Access](/images/release_to_maven_keychain.png "GPG keychain Access")

right now, all work about GPG KEY is prepared, as supplement, you can check this
[link](http://blog.sonatype.com/2010/01/how-to-generate-pgp-signatures-with-maven) for more details.

## Configure your project module

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

    <!-- and the project description. -->
    <groupId>com.vincestyling</groupId>
    <artifactId>traversaless-viewpager</artifactId>
    <version>4.0.1_r1-SNAPSHOT</version> <!-- here must declare as "-SNAPSHOT" phrase. -->
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

    <!-- Important step : maven will do few commit and tagging current version, so don't forget config scm. -->
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

now we are going to release a snapshot version to the `Staging Repository` :

```shell
$ mvn release:clean
$ mvn release:prepare
$ mvn release:perform
```

in the `prepare` goal, you shall be prompt to type the `GPG passphrase` several times, and maven will tagging your current version,
commit two changes, finally push all modification to `remotes`. and if something wrong with the process, of course you should solve it,
but before you do it again, you must discard those modification if maven does.

```shell
`10a55a3b5b9b4d473e78ccd0c60af0672d7adecd` is commit hash code before releasing.
$ git reset --hard 10a55a3b5b9b4d473e78ccd0c60af0672d7adecd
$ git tag -d TAG_NAME
$ git push origin :refs/tags/TAG_NAME
$ git push --force
```

then perform the "release a snapshot version" operation again.

## Release it in the Sonatype

To release your artifacts, open your favorite browser and go to [https://oss.sonatype.org/](https://oss.sonatype.org/),
of course you should use first step's account to `Log In`, then go to the `Build Promotion` tab's `Staging Repositories`
which in the left pane, find out the Repository that you just release(normally at the list bottom),
click the "Close" button then do the rest.

![Release It](/images/release_to_maven_release_it.png "Release It")

Sometime you'll get the error like 'signature failed' that remind you to solve the GPG Key pair conflict problem,
If happen, you should drop the artifacts, then re-release again.
ensure the "Activity" field says "Last operation completed successfully" before you move on.

![Release It](/images/release_to_maven_release_it_errors.png "Release It")

if you didn't see any error in the bottom "Activity" pane, you'll see the "Release" button enable quickly,
keep Refresh the Repositories if you didn't get it, now it's time to do the final step :

![Release It](/images/release_to_maven_release_it_final.png "Release It")

Congratulation, you've done all job about release, after you successfully release, your component will be published to Central,
the best way to validate whether success or not is search your artifacts in [search.maven.org](search.maven.org),
but it may take time before your artifacts appear, typically within 10 minutes even hours, so be patient with it.

I follow this [guide](http://central.sonatype.org/pages/ossrh-guide.html) all the time,
and [this](https://docs.sonatype.org/pages/diffpages.action?pageId=6619209&originalId=13598740),
as supplement, below is my two release ticket to record.

[OSSRH-9739](https://issues.sonatype.org/browse/OSSRH-9739)
[OSSRH-10903](https://issues.sonatype.org/browse/OSSRH-10903)
