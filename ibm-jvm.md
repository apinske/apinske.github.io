# IBM JVM Tools

* GC (verbosegc)
    * [IBM Pattern Modeling and Analysis Tool for Java Garbage Collector](https://www.ibm.com/support/pages/ibm-pattern-modeling-and-analysis-tool-java-garbage-collector-pmat)
    * Download: [ga458.jar](https://public.dhe.ibm.com/software/websphere/appserv/support/tools/pmat/ga458.jar)
    * Heap (JDK 8, gencon)
        * nursery (Xmns, Xmnx) - short-lived
        * tenured (Xms, Xmx) - long-lived
* Thread dumps (javacore)
    * [IBM Thread and Monitor Dump Analyzer for Java](https://www.ibm.com/support/pages/ibm-thread-and-monitor-dump-analyzer-java-tmda)
    * Download: [jca467.jar](https://public.dhe.ibm.com/software/websphere/appserv/support/tools/jca/jca467.jar)
* Heap dumps (phd)
    * [IBM HeapAnalyzer](https://www.ibm.com/support/pages/ibm-heapanalyzer)
    * Download: [ha457.jar](https://public.dhe.ibm.com/software/websphere/appserv/support/tools/HeapAnalyzer/ha457.jar)

# general JVM Tools

* MBean Explorer, Flight Recorder
    * Mission Control, JConsole
    * [hawt.io](https://hawt.io/docs/get-started/)
        * deploy [jolokia](https://jolokia.org/download.html)
        * run `java -jar -Dhawtio.proxyDisableCertificateValidation=true -Dhawtio.proxyAllowlist=* hawtio-app-2.10.1.jar`
