# Java Basis Ökosystem

## Basics
* Language: Java, ...
* JRE (Java Runtime Environment)
    * JVM (C++) - Java Virtual Machine
    * Class-Library (Java) - Java SE
    * Supporting Files (Timezone Database, ...)
* JDK (Java Development Kit)
    * &JRE
    * Compiler
    * Tools (jar, ...)

## JVM 
* [Spec](https://docs.oracle.com/javase/specs/index.html)
* Class-Files, Bytecode-Interpreter
* Garbage Collector, JIT-Compiler
* Implementierungen
    * "Hotspot" (Teil von OpenJDK, ursprünglich nur Name des JIT-Compilers)
    * OpenJ9 ([JVM](https://github.com/eclipse-openj9/openj9), [JDK](https://github.com/ibmruntimes/openj9-openjdk-jdk)): a JVM for OpenJDK
    * Graal (JIT, AOT)
    * Android: Dalvik - JIT, Android Runtime (ART) - AOT

## JDK Distros
* Upstream: [OpenJDK](https://github.com/openjdk/jdk) - reference implementation (RI)
    * open-sourced by Sun in 2007
    * Hotspot, Class-Lib
    * nur neueste Version, z.B. [16](https://jdk.java.net/16/), Releases: 6 Monate, 3 Jahre LTS (11, 17, ...)
* [AdoptOpenJDK](https://adoptopenjdk.net) (bald Adoptium) mit Hostpot oder OpenJ9
    * [build scripts](https://github.com/adoptium/temurin-build)
    * [GitHub Action](https://github.com/apinske/simpleos/actions/workflows/jdk.yaml)
* [Azul Zulu](https://www.azul.com/downloads/?package=jdk#download-openjdk)
* Amazon Corretto
* Azul Zing
* Oracle
* IBM (nur bis 8)
* SAP
* ...
