---
title: 'Do usługi uwierzytelniania: Data Lake Store za pomocą usługi Azure Active Directory w języku Java | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie usługi do usługi z usługi Data Lake Store za pomocą usługi Azure Active Directory z językiem Java
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c8ef983871f3fb1ec47522571ce95843bdd2d313
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625869"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-java"></a>Do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą języka Java
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

W tym artykule opisano sposób użycia zestawu Java SDK w celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Store. Uwierzytelnianie użytkownika końcowego przy użyciu zestawu SDK Java usługi Data Lake Store nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Należy wykonać czynności opisane w [do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilacji bez używania systemu kompilacji, takiego jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.

* (Opcjonalnie) Wtyczka [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) przypominająca środowisko IDE lub podobna.

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami
1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Dodaj poniższe zależności do Twojego pliku **pom.xml** programu Maven. Dodaj poniższy fragment kodu przed tagiem **\</project>**:
   
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
   
    Pierwszą zależnością jest użycie zestawu SDK usługi Data Lake Store (`azure-data-lake-store-sdk`) z repozytorium maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Zestaw SDK usługi Data Lake Store używa fasady rejestrowania [slf4j](http://www.slf4j.org/), która umożliwia wybór spośród wielu popularnych struktur rejestrowania, takich jak log4j, rejestrowanie Java, logback itp. lub brak rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](http://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Użyj następującego fragmentu kodu w aplikacji Java można uzyskać tokenu dla aplikacji Active Directory w sieci Web utworzony wcześniej przy użyciu jednej z podklasy `AccessTokenProvider` (w poniższym przykładzie użyto `ClientCredsTokenProvider`). Dostawca tokenu zapisuje w pamięci poświadczenia użyte do uzyskania tokenu i automatycznie odnawia token, gdy zbliża się czas jego wygaśnięcia. Istnieje możliwość utworzenia własnych podklasy `AccessTokenProvider` tak tokeny są uzyskiwane przez kod klienta. Teraz załóżmy po prostu użyj w zestawie SDK.

    Zastąp wartość **FILL-IN-HERE** (Wypełnij tutaj) rzeczywistymi wartościami dla aplikacji sieci Web usługi Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Zestaw SDK usługi Data Lake Store udostępnia wygodne metody zarządzania tokenami zabezpieczającymi, które są potrzebne do komunikacji z kontem usługi Data Lake Store. Jednak zestaw SDK nie zmusza do używania tylko tych metod. Możesz również używać jakichkolwiek innych sposobów uzyskiwania tokenu, na przykład przy użyciu [zestawu SDK usługi Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) lub własnego kodu niestandardowego.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania użytkowników końcowych do uwierzytelniania w usłudze Azure Data Lake Store przy użyciu zestawu SDK Java. Teraz można przeglądać następujące artykuły, które porozmawiać na temat sposobu korzystania z zestawu Java SDK do pracy z usługą Azure Data Lake Store.

* [Operacje na danych w usłudze Data Lake Store przy użyciu zestawu SDK Java](data-lake-store-get-started-java-sdk.md)


