---
layout: post
title: Reproducible Builds in Java
author: Maria Camenzuli
---

When it comes to software, we look at source code to learn what an application will do when executed. However, the source code we read is not what will actually be executed by our computers. In the Java world, it is the Java bytecode that is produced by our compilers that eventually gets executed, normally after having been packaged into an archive. So when we are given code that has already been compiled and packaged, how can we verify that it is indeed the product of the source code we think produced it?

The answer to this is reproducible builds! A reproducible build is a deterministic function that, given our source code as input, will produce an executable artifact that is the same, byte for byte, every time it is run on the same input.

## What is the value of a reproducible build?

I personally discovered the need for a reproducible build when I was tasked with implementing an automated change management system at work. Our production system, which is Java based, for the most part, is frequently audited. The way this works is that every time the auditors come in, we need to provide them with a checksum of the software artifacts running on production at the time. They then compare these checksums with the checksums from the previous audit to determine which artifacts where modified, and follow up with us on the changes detected. We therefore want to make sure that the checksums of software artifacts only change when a change in the source code has been made. If someone rebuilds and redeploys an artifact, that should not be flagged as a change in the live system, so as not to waste anybody's time.

Apart from this, a reproducible build enables you to do more with your build tools. For example, if you have a build that produces multiple artifacts, without being able to detect which of the artifacts has changed after every build, you either automatically deploy all artifacts, or none at all. A reproducible build would allow your tools to recognize what has changed and deploy accordingly.

## Is the standard Java build reproducible?

We can get the answer to that question by running a simple test with Gradle, one of the two most commonly used Java build tools.

Let's open up a terminal and create a simple Java project with Gradle.

`> mkdir reproducible-build-test`

`> cd reproducible-build-test`

`> gradle init --type java-application`

This will generate a simple Java command line application that prints out 'Hello world'. Next, we will build this project and take a checksum of the resulting jar file.

`> gradle build`

`> md5sum build/libs/reproducible-build-test.jar`

Finally, we clean our project and rebuild it, then take a checksum of the rebuilt jar.

`> gradle clean`

`> gradle build`

`> md5sum build/libs/reproducible-build-test.jar`

If you have followed these steps, you will note that we got 2 different checksums, even though we built the exact same source code twice. You can try the same experiment with a simple Maven project, but the result will be the same. We can therefore conclude that no, standard Java builds are not reproducible.

## The makings of a jar file

To build our jar file, Gradle began by creating `.class` files from our `.java` files by compiling them into Java bytecode. Then, these files were packaged together with some metadata to form a jar archive. This is quite a simple 2-step process, so with one more test, we can find out which part of the build is non-deterministic.

Let's put aside Gradle and use the Java compiler `javac` dirctly to compile a class, which we will checksum, recompile, and checksum again.

`> javac src/main/java/App.java`

`> md5sum src/main/java/App.class`

`> rm src/main/java/App.class`

`> javac src/main/java/App.java`

`> md5sum src/main/java/App.class`

This time the checksums are the same. We now know that `javac` is deterministic, so we can therefore conclude that the non-determinisim in our Java build has to be introduced while the jar file is being packaged.

To quote the [official Java documentation](https://docs.oracle.com/javase/8/docs/technotes/guides/jar/jar.html#Intro), a jar file is "essentially a zip file that contains an optional META-INF directory", and this is the root of our problem. It turns out that the specification of zip files requires every entry in the zip file to include a local file modification timestamp. This means that every time we recompile and repackage our Java project, we will get a different jar file because there is a difference in file modified timestamps.

Apart from this, additional non-determinism can be introduced if files are put into the archive in different orders. This can happen in the case of parallel builds, or possibly even if you run the same build on different operating systems.

## Are reproducible builds in Java possible?

Yes. To make a Java build reproducible, we need to tweak it so that the files that make up the jar archive are always packaged in the same order, and that all timestamps are set to a constant date and time.

Luckily for us, Gradle actually introduced support for reproducible builds starting from [version 3.4](https://docs.gradle.org/3.4/release-notes.html#initial-support-for-reproducible-archives). If you build your Java project using Gradle, you can specify that you want your archive generating tasks to use a reproducilbe file order and to discard timestamps by adding the following to your build script.

```
tasks.withType(AbstractArchiveTask) {
    preserveFileTimestamps = false
    reproducibleFileOrder = true
}
```

You can read more about this in the Gradle [user guide](https://docs.gradle.org/3.4/userguide/working_with_files.html#sec:reproducible_archives).

For the Maven users, I am not aware of any way to get the archiver to generate reproducible jars. However, there is a [reproducible build plugin](https://zlika.github.io/reproducible-build-maven-plugin/) available that will uncompress jars and repackage them for you, sorting the contents and replacing varying timestamps with a constant.

***Note on using Gradle reproducible builds with Spring Boot versions predating version 2:*** *The Spring Boot Gradle plugin repackages your jar file after Gradle packages it to add in the things it needs to make your project executable. This repackaging step does not support preserving file order and setting constant timestamps. This [issue has been solved for Spring Boot 2](https://github.com/spring-projects/spring-boot/issues/8391).*

## Conclusion

We have discussed what a reproducible build is, and seen that it provides value by enabling verification of software artifacts and supporting automated build tools. Although standard Java builds are non-deterministic because of the specification of zip files, there are workarounds that enable us to achieve reproducible builds in Java. If you want to read more about reproducible builds, I suggest you take a look at [reproducible-builds.org](https://reproducible-builds.org/).