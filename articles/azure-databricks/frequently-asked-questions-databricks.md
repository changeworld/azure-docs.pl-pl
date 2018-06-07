---
title: 'Azure Databricks: Typowe pytania i pomoc | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania i informacje dotyczące rozwiązywania problemów dotyczących Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: cd231a6baad74cd88d82b219e2e58e9252187ff5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599001"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Często zadawane pytania dotyczące usługi Azure Databricks

W tym artykule wymieniono najważniejszych zapytań, może być związany Azure Databricks. Zawiera także listę niektórych typowych problemów, które mogą mieć podczas używania Databricks. Aby uzyskać więcej informacji, zobacz [co to jest Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>Czy można użyć własnych kluczy szyfrowania lokalnego? 
W bieżącej wersji za pomocą własnych kluczy z usługi Azure Key Vault jest nieobsługiwane. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Z Databricks można używać sieci wirtualnych platformy Azure?
Nowa sieć wirtualna jest utworzona w ramach Databricks inicjowania obsługi administracyjnej. W tej wersji nie można użyć własnych sieci wirtualnej platformy Azure.

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Jak dostęp do usługi Azure Data Lake Store z notesu? 

Wykonaj następujące kroki:
1. W usłudze Azure Active Directory (Azure AD) udostępnić nazwy głównej usługi i zarejestruj jego klucza.
2. Przypisz uprawnienia niezbędne do nazwy głównej usługi w usłudze Data Lake Store.
3. Dostęp do pliku w usłudze Data Lake Store, użyj poświadczenia główne w Notatniku.

Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Store z Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Rozwiązywania typowych problemów

Poniżej przedstawiono kilka problemów, które mogą wystąpić z Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problem: Ta subskrypcja nie jest zarejestrowany do korzystania z przestrzeni nazw "Microsoft.Databricks"

#### <a name="error-message"></a>Komunikat o błędzie

"Ta subskrypcja nie jest zarejestrowany do używania przestrzeni nazw"Microsoft.Databricks". Zobacz https://aka.ms/rps-not-found dotyczące sposobu rejestrowania subskrypcji. (Code: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Rozwiązanie

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **subskrypcje**, subskrypcja jest używany, a następnie **dostawców zasobów**. 
3. Lista dostawców zasobów przed **Microsoft.Databricks**, wybierz pozycję **zarejestrować**. Musi mieć rolę współautora lub właściciela subskrypcji można zarejestrować dostawcy zasobów.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: Twoje konto {e-mail} nie ma roli właściciela lub współautora Databricks zasobu obszaru roboczego w portalu Azure

#### <a name="error-message"></a>Komunikat o błędzie

"Konto {e-mail} nie ma roli właściciela lub współautora Databricks zasobu obszaru roboczego w portalu Azure. Ten błąd może również wystąpić, jeśli użytkownik-Gość w dzierżawie. Poproś administratora o udzielenie dostępu lub dodanie Ciebie jako użytkownik bezpośrednio w obszarze roboczym Databricks." 

#### <a name="solution"></a>Rozwiązanie

Poniżej przedstawiono kilka rozwiązania tego problemu:

* Aby zainicjować dzierżawcy, użytkownik musi zalogować się jako zwykły użytkownik dzierżawy, a nie jako Gość. Rola współautora musi mieć również Databricks zasobu obszaru roboczego. Można udzielić użytkownikowi dostępu z **(IAM) kontroli dostępu** kartę w obszarze roboczym Databricks w portalu Azure.

* Ten błąd może również wystąpić, jeśli nazwy domeny poczty e-mail jest przypisany do wielu katalogów w usłudze Azure AD. Aby obejść ten problem, należy utworzyć nowego użytkownika w katalogu, który zawiera subskrypcji z obszaru roboczego Databricks.

    a. W portalu Azure przejdź do usługi Azure AD. Wybierz **użytkowników i grup** > **dodać użytkownika**.

    b. Dodaj użytkownika z `@<tenant_name>.onmicrosoft.com` e-mail zamiast `@<your_domain>` wiadomości e-mail. Można znaleźć tej opcji w **domen niestandardowych**, w obszarze usługi Azure AD w portalu Azure.
    
    c. Przyznaj tym nowego użytkownika **współautora** roli Databricks zasobu obszaru roboczego.
    
    d. Zaloguj się do portalu Azure za pomocą nowego użytkownika, a następnie znajdź obszar roboczy Databricks.
    
    e. Uruchom obszaru roboczego Databricks jako ten użytkownik.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: Twoje konto {e-mail} nie została zarejestrowana w Databricks 

#### <a name="solution"></a>Rozwiązanie

Jeśli nie może utworzyć obszaru roboczego i są dodawane jako użytkownik, skontaktuj się z osobą, która utworzyła obszaru roboczego. Mieć tej osoby, które można dodać przy użyciu konsoli administracyjnej Databricks Azure. Aby uzyskać instrukcje, zobacz [Dodawanie i zarządzanie użytkownikami](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Jeśli utworzono obszar roboczy i nadal ten błąd, spróbuj wybrać **zainicjować obszaru roboczego** ponownie z portalu Azure.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problem: Błąd uruchamiania dostawcy chmury podczas konfigurowania klastra (PublicIPCountLimitReached)

#### <a name="error-message"></a>Komunikat o błędzie

"Błąd uruchamiania dostawcy usług w chmurze: Napotkano błąd dostawcy chmury podczas konfigurowania klastra. Aby uzyskać więcej informacji zobacz Podręcznik Databricks. Kod błędu systemu Azure: PublicIPCountLimitReached. Komunikat o błędzie platformy Azure: nie można utworzyć więcej niż 60 publicznych adresów IP dla tej subskrypcji w tym regionie. "

#### <a name="solution"></a>Rozwiązanie

Dzięki klastrom Databricks jeden publiczny adres IP na węzeł. Jeśli subskrypcja jest już używana wszystkich swoich publicznych adresów IP, należy [żądania, aby zwiększyć przydział](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Wybierz **przydziału** jako **wydawania typu**, i **sieci: ARM** jako **typu przydziału**. W **szczegóły**, zażądać zwiększenia limitu przydziału publicznego adresu IP. Na przykład jeśli limit jest obecnie 60, a ma zostać utworzony 100 węzła klastra, zażądać zwiększenia limitu do 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problem: Drugi typ błąd uruchamiania dostawcy chmury podczas konfigurowania klastra (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Komunikat o błędzie

"Błąd uruchamiania dostawcy usług w chmurze: Napotkano błąd dostawcy chmury podczas konfigurowania klastra. Aby uzyskać więcej informacji zobacz Podręcznik Databricks.
Kod błędu systemu Azure: komunikat o błędzie platformy MissingSubscriptionRegistration Azure: subskrypcji nie jest zarejestrowany w celu używania przestrzeni nazw "Microsoft.Compute". Zobacz https://aka.ms/rps-not-found dotyczące sposobu rejestrowania subskrypcji. "

#### <a name="solution"></a>Rozwiązanie

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **subskrypcje**, subskrypcja jest używany, a następnie **dostawców zasobów**. 
3. Lista dostawców zasobów przed **Microsoft.Compute**, wybierz pozycję **zarejestrować**. Musi mieć rolę współautora lub właściciela subskrypcji można zarejestrować dostawcy zasobów.

Aby uzyskać szczegółowe instrukcje, zobacz [dostawców zasobów i typów](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problem: Azure Databricks wymaga uprawnień dostępu do zasobów w organizacji, którzy mogą udzielić tylko administrator.

#### <a name="background"></a>Tło

Azure Databricks jest zintegrowany z usługą Azure AD. Dzięki temu można ustawić uprawnień w ramach Azure Databricks (na przykład na notesów lub klastry), określając użytkowników z usługi Azure AD. Dla Databricks Azure można było wyświetlić listę nazw użytkowników z usługi Azure AD wymaga uprawnienia do odczytu do tych informacji. Wymaga to zgodę. Jeśli zgody nie jest już dostępny, zostanie wyświetlony błąd.

#### <a name="solution"></a>Rozwiązanie

Zaloguj się jako administrator globalny do portalu Azure. Dla usługi Azure Active Directory, przejdź do **ustawienia użytkownika** karcie i upewnij się, że **użytkowników można wyrazić zgodę na dostęp do danych firmowych w ich imieniu aplikacji** ustawiono **tak**.

## <a name="next-steps"></a>Kolejne kroki

- [Szybki Start: Rozpoczynanie pracy z Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Co to jest Azure Databricks?](what-is-azure-databricks.md)

