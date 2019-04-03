---
title: 'Service-to-service authentication: Java za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnąć uwierzytelniania service to service za pomocą usługi Azure Data Lake Storage Gen1 za pomocą usługi Azure Active Directory za pomocą języka Java
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c32eada2acca73e089c2296ce8e59c529d7af665
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879169"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Uwierzytelnianie Service to service za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu języka Java
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

W tym artykule dowiesz się o tym, jak używać zestawu SDK dla języka Java celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1. Uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu SDK języka Java nie jest obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Zostały wykonane kroki opisane w [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilacji bez używania systemu kompilacji, takiego jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.

* (Opcjonalnie) Środowisko IDE, takie jak [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) lub podobne.

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami
1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

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
   
    Pierwszą zależnością jest użycie zestawu SDK usługi Data Lake Storage Gen1 (`azure-data-lake-store-sdk`) z repozytorium maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Korzysta z zestawu SDK usługi Data Lake Storage Gen1 [slf4j](https://www.slf4j.org/) fasady rejestrowania, które umożliwia wybór spośród wielu popularnych struktur rejestrowania, takich jak log4j, rejestrowanie Java, logback itp., lub Brak rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](https://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Aby uzyskać token dla utworzonej wcześniej przy użyciu jednej z podklas dla aplikacji sieci Web usługi Active Directory za pomocą następującego fragmentu kodu w aplikacji Java `AccessTokenProvider` (w poniższym przykładzie użyto `ClientCredsTokenProvider`). Dostawca tokenu zapisuje w pamięci poświadczenia użyte do uzyskania tokenu i automatycznie odnawia token, gdy zbliża się czas jego wygaśnięcia. Można utworzyć własne podklasy `AccessTokenProvider` aby tokeny były uzyskiwane przez kod klienta. Na razie Przyjrzyjmy po prostu używać dostarczanej ścieżki w zestawie SDK.

    Zastąp wartość **FILL-IN-HERE** (Wypełnij tutaj) rzeczywistymi wartościami dla aplikacji internetowej usługi Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Zestaw SDK usługi Data Lake Storage Gen1 udostępnia wygodne metody, które umożliwiają zarządzanie tokeny zabezpieczające, potrzebne do komunikacji z kontem Data Lake Storage Gen1. Jednak zestaw SDK nie zmusza do używania tylko tych metod. Możesz również używać jakichkolwiek innych sposobów uzyskiwania tokenu, na przykład przy użyciu [zestawu SDK usługi Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) lub własnego kodu niestandardowego.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób użycia uwierzytelniania użytkowników końcowych do uwierzytelniania w usłudze Data Lake Storage Gen1 przy użyciu zestawu SDK języka Java. Możesz teraz przejrzeć następujące artykuły, które omówiono sposób korzystania z zestawu SDK Java do pracy z Data Lake Storage Gen1.

* [Operacje na danych na Data Lake Storage Gen1 przy użyciu zestawu SDK języka Java](data-lake-store-get-started-java-sdk.md)


