# Java

## Basics
* JRE (Java Runtime Environment)
    * JVM (C++) - Java Virtual Machine
    * Class-Library (Java) - Java SE
    * Supporting Files (Timezone Database, ...)
* Languages: Java, Groovy, Kotlin, Scala, ...
* JDK (Java Development Kit)
    * &JRE
    * Compiler
    * Tools (jar, ...)

## JVM 
* [Spec](https://docs.oracle.com/javase/specs/index.html)
* Class-Files, Bytecode-Interpreter
* Garbage Collector, JIT-Compiler
* Implementierungen
    * Hotspot (Teil von [OpenJDK](https://github.com/openjdk/jdk), ursprünglich nur Name des JIT-Compilers)
    * OpenJ9 ([JVM](https://github.com/eclipse-openj9/openj9), [JDK](https://github.com/ibmruntimes/openj9-openjdk-jdk)): a JVM for OpenJDK
    * Graal (JIT, AOT)
    * Android: Dalvik - JIT, Android Runtime (ART) - AOT

## JDK Distros
* Upstream: [OpenJDK](https://github.com/openjdk/jdk) - reference implementation (RI)
    * open-sourced by Sun in 2007
    * Hotspot, Class-Lib
    * nur neueste Version, z.B. [18](https://jdk.java.net/18/), Release-Zyklus: 6 Monate, 3 Jahre LTS (11, 17, ...)
* [Adoptium Temurin](https://adoptium.net/temurin/releases) mit Hostpot
    * [build scripts](https://github.com/adoptium/temurin-build)
* [Azul Zulu](https://www.azul.com/downloads/?package=jdk#download-openjdk)
* [IBM Semeru](https://developer.ibm.com/languages/java/semeru-runtimes/downloads/) mit OpenJ9
* Amazon Corretto
* Azul Zing
* Oracle
* IBM (nur bis 8)
* SAP
* ...
