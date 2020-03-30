---
title: Uwierzytelnianie usługi do usługi — Data Lake Storage Gen1 — Java SDK
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory w języku Java
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904536"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Uwierzytelnianie usługi do usługi przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu języka Java

> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

W tym artykule dowiesz się, jak używać java SDK do uwierzytelniania usługi do usługi za pomocą usługi Azure Data Lake Storage Gen1. Uwierzytelnianie użytkownika końcowego przy użyciu usługi Data Lake Storage Gen1 przy użyciu zestawu Java SDK nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji sieci Web usługi Azure Active Directory**. Kroki w [uwierzytelnianiu usługi do usługi za pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż jest to możliwe do kompilacji bez użycia systemu kompilacji, takich jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.

* (Opcjonalnie) IDE jak [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) lub podobne.

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Dodaj poniższe zależności do Twojego pliku **pom.xml** programu Maven. Dodaj następujący fragment kodu przed tagiem ** \</project>:**

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    Pierwszą zależnością jest użycie SDK gen1`azure-data-lake-store-sdk`magazynu danych Lake Gen1 ( ) z repozytorium maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Zestaw SDK data lake storage Gen1 używa fasady rejestrowania [slf4j,](https://www.slf4j.org/) która umożliwia wybór spośród wielu popularnych struktur rejestrowania, takich jak log4j, rejestrowanie w języku Java, logowanie itp., lub brak rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](https://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Użyj następującego fragmentu kodu w aplikacji Java, aby uzyskać token dla aplikacji sieci Web usługi `AccessTokenProvider` Active Directory `ClientCredsTokenProvider`utworzonej wcześniej przy użyciu jednej z podklas (poniższy przykład używa ). Dostawca tokenu zapisuje w pamięci poświadczenia użyte do uzyskania tokenu i automatycznie odnawia token, gdy zbliża się czas jego wygaśnięcia. Istnieje możliwość tworzenia własnych `AccessTokenProvider` podklas, więc tokeny są uzyskiwane przez kod klienta. Na razie po prostu użyjmy tego dostarczonego w SDK.

    Zastąp wartość **FILL-IN-HERE** (Wypełnij tutaj) rzeczywistymi wartościami dla aplikacji internetowej usługi Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Zestaw SDK usługi Data Lake Storage Gen1 SDK udostępnia wygodne metody, które umożliwiają zarządzanie tokenami zabezpieczającymi potrzebnymi do rozmowy z kontem Data Lake Storage Gen1. Jednak zestaw SDK nie zmusza do używania tylko tych metod. Możesz również używać jakichkolwiek innych sposobów uzyskiwania tokenu, na przykład przy użyciu [zestawu SDK usługi Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) lub własnego kodu niestandardowego.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać uwierzytelniania użytkowników końcowych do uwierzytelniania przy użyciu usługi Data Lake Storage Gen1 przy użyciu zestawu Java SDK. Teraz możesz przeglądać następujące artykuły, które mówią o tym, jak używać zestawu Java SDK do pracy z pamięcią Data Lake Storage Gen1.

* [Operacje danych w pamięci masowej danych Gen1 przy użyciu zestawu Java SDK](data-lake-store-get-started-java-sdk.md)
