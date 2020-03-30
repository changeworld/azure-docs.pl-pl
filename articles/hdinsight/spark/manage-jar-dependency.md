---
title: Zarządzanie zależnościami JAR — Usługa Azure HDInsight
description: W tym artykule omówiono najważniejsze wskazówki dotyczące zarządzania zależnościami archiwum Java (JAR) dla aplikacji HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135735"
---
# <a name="jar-dependency-management-best-practices"></a>Najlepsze rozwiązania dotyczące zarządzania zależnościami JAR

Składniki zainstalowane w klastrach USŁUGI HDInsight mają zależności od bibliotek innych firm. Zazwyczaj określona wersja wspólnych modułów, takich jak Guawa, odwołuje się do tych wbudowanych komponentów. Podczas przesyłania aplikacji z jego zależności, może to spowodować konflikt między różnymi wersjami tego samego modułu. Jeśli wersja składnika, do której odwołujesz się w ścieżce klasy pierwszy, wbudowane składniki mogą zgłaszać wyjątki z powodu niezgodności wersji. Jeśli jednak wbudowane składniki najpierw wstrzykną swoje zależności do ścieżki `NoSuchMethod`klasy, aplikacja może zgłaszać błędy, takie jak .

Aby uniknąć konfliktu wersji, należy rozważyć cieniowanie zależności aplikacji.

## <a name="what-does-package-shading-mean"></a>Co oznacza cieniowanie pakietów?
Cieniowanie umożliwia uwzględnienie i zmianę nazwy zależności. Przenosi klasy i przepisuje kod bajtowy i zasoby, których dotyczy problem, aby utworzyć prywatną kopię zależności.

## <a name="how-to-shade-a-package"></a>Jak zacienić paczkę?

### <a name="use-uber-jar"></a>Użyj uber-jar
Uber-jar to pojedynczy plik słoika, który zawiera zarówno słoik aplikacji, jak i jego zależności. Zależności w Uber-jar domyślnie nie są cieniowane. W niektórych przypadkach może to spowodować konflikt wersji, jeśli inne składniki lub aplikacje odwołują się do innej wersji tych bibliotek. Aby tego uniknąć, możesz utworzyć plik Uber-Jar z niektórymi (lub wszystkimi) zależnymi zacienionymi.

### <a name="shade-package-using-maven"></a>Pakiet cieniowania za pomocą Maven
Maven może tworzyć aplikacje napisane zarówno w języku Java, jak i Scala. Maven-shade-plugin może pomóc w stworzeniu cienia uber-jar łatwo.

Poniższy przykład pokazuje `pom.xml` plik, który został zaktualizowany do cienia pakietu za pomocą maven-shade-plugin.  Sekcja `<relocation>…</relocation>` XML przenosi klasy `com.google.guava` z `com.google.shaded.guava` pakietu do pakietu, przenosząc odpowiednie wpisy pliku JAR i przepisując kod bajtowy, którego dotyczy problem.

Po `pom.xml`zmianie można `mvn package` wykonać, aby zbudować zacieniony uber-jar.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Pakiet shade za pomocą SBT
SBT jest również narzędziem kompilacji dla Scala i Java. SBT nie ma wtyczki cień jak maven-shade-plugin. Można zmodyfikować `build.sbt` pakiety plików do cienia. 

Na przykład, `com.google.guava`aby zacienić , `build.sbt` można dodać poniższe polecenie do pliku:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Następnie można `sbt clean` uruchomić `sbt assembly` i zbudować zacieniowany plik jar. 

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z narzędzi HDInsight Intellij](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Tworzenie aplikacji Scala Maven dla platformy Spark w technologii IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
