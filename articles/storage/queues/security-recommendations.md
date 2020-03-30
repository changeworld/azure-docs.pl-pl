---
title: Zalecenia dotyczące zabezpieczeń magazynu kolejek
titleSuffix: Azure Storage
description: Dowiedz się więcej o zaleceniach dotyczących zabezpieczeń dla magazynu kolejek. Wdrożenie tych wskazówek pomoże Ci wypełnić swoje zobowiązania w zakresie bezpieczeństwa, zgodnie z opisem w naszym modelu wspólnej odpowiedzialności.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060898"
---
# <a name="security-recommendations-for-queue-storage"></a>Zalecenia dotyczące zabezpieczeń magazynu kolejek

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń magazynu kolejek. Wdrożenie tych zaleceń pomoże Ci wypełnić swoje zobowiązania w zakresie bezpieczeństwa opisane w naszym modelu wspólnej odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft robi, aby wypełniać obowiązki dostawcy usług, zobacz [Współużytkowane obowiązki związane z przetwarzaniem w chmurze](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Niektóre z zaleceń zawartych w tym artykule mogą być automatycznie monitorowane przez usługę Azure Security Center. Usługa Azure Security Center to pierwsza linia ochrony w ochronie zasobów na platformie Azure. Aby uzyskać informacje na temat usługi Azure Security Center, zobacz [What is Azure Security Center?](../../security-center/security-center-intro.md).

Usługa Azure Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu rozwiązania ich. Aby uzyskać więcej informacji na temat zaleceń usługi Azure Security Center, zobacz [Zalecenia dotyczące zabezpieczeń w usłudze Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Korzystanie z modelu wdrażania usługi Azure Resource Manager | Tworzenie nowych kont magazynu przy użyciu modelu wdrażania usługi Azure Resource Manager w celu uzyskania ważnych ulepszeń zabezpieczeń, w tym doskonałej kontroli dostępu (RBAC) i inspekcji, wdrażania i nadzoru opartego na Menedżerze zasobów, dostępu do zarządzanych tożsamości, dostępu do usługi Azure Key Vault dla wpisów tajnych i uwierzytelniania opartego na usłudze Azure AD i autoryzacji dostępu do danych i zasobów usługi Azure Storage. Jeśli to możliwe, należy przeprowadzić migrację istniejących kont magazynu, które używają klasycznego modelu wdrażania do korzystania z usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview). | - |
| Włącz opcję **Bezpieczne przeniesienie wymagane** na wszystkich kontach magazynu | Po włączeniu opcji **Wymagany transfer bezpieczny** wszystkie żądania na koncie magazynu muszą odbywać się za zabezpieczone połączenia. Wszelkie żądania wykonane za pośrednictwem protokołu HTTP zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md). | [Tak](../../security-center/security-center-sql-service-recommendations.md) |
| Włącz zaawansowaną ochronę przed zagrożeniami dla wszystkich kont magazynu | Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania. Alerty zabezpieczeń są wyzwalane w usłudze Azure Security Center, gdy występują anomalie w działaniu, a także są wysyłane za pośrednictwem poczty e-mail do administratorów subskrypcji, ze szczegółami podejrzanej aktywności i zaleceniami dotyczącymi sposobu badania i korygować zagrożenia. Aby uzyskać więcej informacji, zobacz [Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage](../common/storage-advanced-threat-protection.md). | [Tak](../../security-center/security-center-sql-service-recommendations.md) |
| Ograniczanie tokenów sygnatury dostępu współdzielonego (SAS) tylko do połączeń HTTPS | Wymaganie protokołu HTTPS, gdy klient używa tokenu sygnatury dostępu do danych kolejki, pomaga zminimalizować ryzyko podsłuchiwania. Aby uzyskać więcej informacji, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../common/storage-sas-overview.md) | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Autoryzowanie dostępu do danych kolejki za pomocą usługi Azure Active Directory (Azure AD) | Usługa Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia za pomocą klucza udostępnionego do autoryzowania żądań do magazynu kolejki. Aby uzyskać więcej informacji, zobacz [Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](../common/storage-auth-aad.md). | - |
| Należy pamiętać o zasadzie co najmniej uprawnień podczas przypisywania uprawnień do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem funkcji RBAC | Podczas przypisywania roli do użytkownika, grupy lub aplikacji, przyznać tej jednostki zabezpieczeń tylko te uprawnienia, które są niezbędne do wykonywania swoich zadań. Ograniczenie dostępu do zasobów pomaga zapobiegać zarówno niezamierzonemu, jak i złośliwemu niewłaściwemu niewłaściwemu niewłaściwemu wykorzystaniu danych. | - |
| Zabezpieczanie kluczy dostępu do konta za pomocą usługi Azure Key Vault | Firma Microsoft zaleca używanie usługi Azure AD do autoryzowania żądań do usługi Azure Storage. Jeśli jednak musisz użyć autoryzacji klucza udostępnionego, zabezpiecz klucze konta za pomocą usługi Azure Key Vault. Klucze można pobrać z magazynu kluczy w czasie wykonywania, zamiast zapisywać je za pomocą aplikacji. | - |
| Okresowo regeneruj klucze konta | Obracanie kluczy konta okresowo zmniejsza ryzyko ujawnienia danych złośliwym podmiotom. | - |
| Należy pamiętać o zasadzie co najmniej uprzywilejowanych podczas przypisywania uprawnień do sygnatury dostępu Współdzielonego | Podczas tworzenia sygnatury dostępu Współdzielonego, należy określić tylko te uprawnienia, które są wymagane przez klienta do wykonywania jego funkcji. Ograniczenie dostępu do zasobów pomaga zapobiegać zarówno niezamierzonemu, jak i złośliwemu niewłaściwemu niewłaściwemu niewłaściwemu wykorzystaniu danych. | - |
| Mieć plan odwołania dla każdego sygnatury dostępu Współdzielonego, który wydajesz klientom | Jeśli sygnatury dostępu Współdzielonego zostanie naruszona, należy odwołać ten sygnatury dostępu Współdzielonego tak szybko, jak to możliwe. Aby odwołać sygnaturę dostępu Współdzielonego delegowania użytkowników, odwołaj klucz delegowania użytkowników, aby szybko unieważnić wszystkie podpisy skojarzone z tym kluczem. Aby odwołać sygnaturę dostępu Współdzielonego usługi skojarzoną z zasadami dostępu przechowywanego, można usunąć zasadę dostępu przechowywanego, zmienić nazwę zasad lub zmienić jej czas wygaśnięcia na czas, który jest w przeszłości. Aby uzyskać więcej informacji, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../common/storage-sas-overview.md)  | - |
| Jeśli sygnatury dostępu współdzielonego usługi nie jest skojarzony z zasadami dostępu przechowywanego, ustaw czas wygaśnięcia na jedną godzinę lub mniej | Nie można odwołać sygnatury dostępu współdzielonego usługi, która nie jest skojarzona z zasadami dostępu przechowywanego. Z tego powodu zaleca się ograniczenie czasu wygaśnięcia, tak aby sygnatury dostępu Współdzielonego był prawidłowy przez godzinę lub mniej. | - |

## <a name="networking"></a>Obsługa sieci

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Włączanie reguł zapory | Skonfiguruj reguły zapory, aby ograniczyć dostęp do konta magazynu do żądań pochodzących z określonych adresów IP lub zakresów lub z listy podsieci w sieci wirtualnej platformy Azure.Configure firewall rules to limit access to your storage account to requests that originate from specified IP addresses or ranges, or from a list of subnets in an Azure Virtual Network (VNet). Aby uzyskać więcej informacji na temat konfigurowania reguł zapory, zobacz [Ustawienia serwera proxy i zapory usługi Azure File Sync](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Zezwalaj zaufanym usługom firmy Microsoft na dostęp do konta magazynu | Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w sieci wirtualnej platformy Azure lub z dozwolonych publicznych adresów IP. Żądania, które są zablokowane obejmują te z innych usług platformy Azure, z witryny Azure portal, z rejestrowania i metryki usług i tak dalej. Możesz zezwolić na żądania z innych usług platformy Azure, dodając wyjątek, aby umożliwić zaufanym usługom firmy Microsoft dostęp do konta magazynu. Aby uzyskać więcej informacji na temat dodawania wyjątku dla zaufanych usług firmy Microsoft, zobacz [Ustawienia serwera proxy i zapory synchronizacji plików azure](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Korzystanie z prywatnych punktów końcowych | Prywatny punkt końcowy przypisuje prywatny adres IP z sieci wirtualnej platformy Azure (VNet) do konta magazynu. Zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pomocą łącza prywatnego. Aby uzyskać więcej informacji na temat prywatnych punktów końcowych, zobacz [Łączenie prywatnie z kontem magazynu przy użyciu prywatnego punktu końcowego platformy Azure](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Ograniczanie dostępu do sieci do określonych sieci | Ograniczenie dostępu do sieci do sieci obsługujących klientów wymagających dostępu zmniejsza narażenie zasobów na ataki sieciowe. | [Tak](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Rejestrowanie/monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Śledzenie sposobu autoryzowania żądań | Włącz rejestrowanie usługi Azure Storage, aby śledzić, jak każde żądanie wykonane w usłudze Azure Storage zostało autoryzowane. Dzienniki wskazują, czy żądanie zostało wykonane anonimowo, przy użyciu tokenu OAuth 2.0, przy użyciu klucza udostępnionego lub przy użyciu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji, zobacz [rejestrowanie analizy usługi Azure Storage](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja zabezpieczeń platformy Azure](https://docs.microsoft.com//azure/security/)
- [Bezpieczna dokumentacja dewelopera](https://docs.microsoft.com/azure/security/develop/).
