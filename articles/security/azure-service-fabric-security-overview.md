---
title: Omówienie zabezpieczeń usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie zabezpieczeń usługi Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610889"
---
# <a name="azure-service-fabric-security-overview"></a>Omówienie zabezpieczeń usługi Azure Service Fabric
[Usługa Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) to platforma systemów rozproszonych ułatwiająca pakowanie, wdrażanie i zarządzanie nimi skalowalnych i niezawodnych mikrousług. Usługa Service Fabric sprostać wyzwaniom związanym z opracowywaniem i zarządzaniem nimi aplikacji w chmurze. Deweloperzy i Administratorzy mogą uniknąć złożonych problemów z infrastrukturą i skoncentrować się na wdrażaniu krytycznych, wymagających obciążeń, które są skalowalne i niezawodne.

W tym artykule przedstawiono zagadnienia dotyczące zabezpieczeń do wdrożenia usługi Service Fabric.

## <a name="secure-your-cluster"></a>Zabezpieczanie klastra
Usługa Azure Service Fabric organizuje usług w klastrze maszyn. Klastry muszą być zabezpieczone uniemożliwiające nieupoważnionym użytkownikom nawiązanie, szczególnie w przypadku, gdy są one uruchamiane obciążenia produkcyjne. Chociaż istnieje możliwość utworzenia z niezabezpieczonym klastrem, dzięki temu anonimowym użytkownikom na łączenie z klastrem (jeśli udostępnia punktów końcowych zarządzania do publicznego Internetu).

Dla klastrów, które są uruchomione, albo samodzielny lub na platformie Azure należy wziąć pod uwagę dwa scenariusze są zabezpieczeń między węzłami i węzeł klienta zabezpieczeń. Różne technologie służy do implementowania tych scenariuszy.

### <a name="node-to-node-security"></a>Węzeł węzeł zabezpieczeń
Węzeł węzeł zabezpieczeń ma zastosowanie do komunikacji między maszynami wirtualnymi lub maszyn w klastrze. Dzięki zabezpieczeniom węzła do węzła tylko te komputery, które są autoryzowane do przyłączenia się do klastra mogą uczestniczyć w hostingu aplikacji i usług w klastrze.

Klastry, które są uruchomione na platformie Azure lub autonomiczny klastrów, które są uruchomione na Windows można użyć dowolnego [certyfikatów zabezpieczeń](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczeń Windows](https://msdn.microsoft.com/library/ff649396.aspx) dla maszyn z systemem Windows Server.

#### <a name="node-to-node-certificate-security"></a>Węzeł węzeł certyfikatów zabezpieczeń

Usługa Service Fabric używa certyfikatów X.509 serwera, określić po utworzeniu klastra. Aby uzyskać szybki przegląd te certyfikaty są i jak można uzyskać lub je utworzyć, zobacz [Praca z certyfikatami](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Certyfikat zabezpieczeń można skonfigurować podczas tworzenia klastra za pośrednictwem witryny Azure portal, szablonów usługi Azure Resource Manager lub szablon JSON autonomicznych. Możesz określić podstawowy certyfikat i opcjonalne certyfikat pomocniczy, który jest używany dla najazdy certyfikatu. Podstawowe i pomocnicze certyfikaty, które określisz powinna być inna niż klienta administratora i certyfikaty klienta tylko do odczytu, które określisz dla [węzeł klienta zabezpieczeń](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Węzeł klienta zabezpieczeń
Węzeł klienta zabezpieczeń można skonfigurować za pomocą tożsamości klienta. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby wiedzieć, którego komputera klienckiego tożsamości, której można zaufać.

Usługa Service Fabric obsługuje dwa typy kontroli dostępu dla klientów, które są podłączone do klastra usługi Service Fabric:

-   **Administrator**: Pełny dostęp do możliwości zarządzania, w tym możliwości odczytu/zapisu.
-   **Użytkownik**: Dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytań), a możliwość usuwania aplikacji i usług.

Za pomocą kontroli dostępu, Administratorzy klastra można ograniczyć dostęp do niektórych typów operacji klastra. To sprawia, że klaster bardziej bezpieczne.

#### <a name="client-to-node-certificate-security"></a>Węzeł klienta certyfikatu zabezpieczeń

Węzeł klienta certyfikatu zabezpieczeń można skonfigurować podczas tworzenia klastra za pośrednictwem witryny Azure portal, szablonów usługi Resource Manager lub szablon JSON autonomicznych. Należy określić certyfikat klienta administracyjnego i/lub certyfikatu klienta użytkownika. Upewnij się, że te certyfikaty różnią się od głównego i dodatkowego certyfikatu, określonych na potrzeby zabezpieczeń między węzłami.

Klienci, którzy łączenia z klastrem przy użyciu certyfikatu administratora mają pełny dostęp do możliwości zarządzania. Klienci, którzy łączą się z klastrem przy użyciu certyfikatu klienta użytkownika tylko do odczytu mają dostęp tylko do odczytu do możliwości zarządzania. Innymi słowy te certyfikaty są używane do kontroli dostępu opartej na rolach (RBAC).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatu w klastrze, zobacz [Konfigurowanie klastra przy użyciu szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Węzeł klienta usługi Azure Active Directory zabezpieczeń

Klastry, które działają na platformie Azure można także zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (Azure AD). Aby uzyskać informacje dotyczące sposobu tworzenia wymaganych artefaktów usługi Azure Active Directory, jak wypełniać je podczas tworzenia klastra i jak połączyć się z tymi klastrami, zobacz [Konfigurowanie klastra przy użyciu szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. Istnieją aplikacje za pomocą interfejsu opartego na sieci web logowania użytkownika i aplikacji w środowisku klienta natywnego.

Klaster usługi Service Fabric udostępnia kilka punktów wejścia do jego funkcje zarządzania, w tym narzędzia Service Fabric Explorer i Visual Studio opartego na sieci web. W związku z tym utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną.

W przypadku klastrów Azure zaleca się używać zabezpieczeń usługi Azure AD do uwierzytelniania klientów i certyfikaty dla zabezpieczeń między węzłami.

W przypadku autonomicznych klastrów systemu Windows Server z systemu Windows Server 2012 R2 i usługi Active Directory zaleca się używać zabezpieczeń Windows za pomocą kont usługi zarządzanych przez grupę (konta Gmsa). W przeciwnym razie za pomocą Windows zabezpieczenia konta Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Omówienie monitorowania i diagnostyki w usłudze Service Fabric
[Monitorowanie i Diagnostyka](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) mają kluczowe znaczenie dla programowania, testowania i wdrażania aplikacji i usług w każdym środowisku. Rozwiązania usługi Service Fabric działają najlepiej, jeśli Implementowanie monitorowania i diagnostyki, aby upewnić się, że aplikacje i usługi działają zgodnie z oczekiwaniami w środowisku programowania lokalnego lub w środowisku produkcyjnym.

Z punktu widzenia zabezpieczeń głównych celów monitorowania i diagnostyki są:

-   Wykrywanie i diagnozowanie problemów sprzętem i infrastrukturą, które może być spowodowany przez zdarzenie związane z zabezpieczeniami.
-   Wykrywanie problemów oprogramowania i aplikacji, które mogą być wskaźnik naruszenia (IoC).
-   Dowiedz się, zużycie zasobów, aby zapobiec nieumyślnemu "odmowa usługi".

Przepływ pracy monitorowania i diagnostyki składa się z trzech kroków:

1.  **Generowanie zdarzeń**: Generowanie zdarzeń zawiera zdarzenia (dzienników, ślady, zdarzenia niestandardowe), zarówno w poziomie infrastruktury (klaster), jak i na poziomie aplikacji/usługi. Przeczytaj więcej na temat [zdarzenia na poziomie infrastruktury](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) i [zdarzenia na poziomie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) Aby zrozumieć, jaka jest dostępna i jak dodać więcej instrumentacji.

2.  **Zdarzenie agregacji**: Wygenerowane zdarzenia muszą być zbierane i zagregowane, zanim będzie można je wyświetlić. Zwykle zaleca się używanie [diagnostyki Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (podobne do zbierania dzienników opartej o agentów) lub [użyciu struktury EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (w procesie zbieranie danych dziennika).

3.  **Analiza**: Zdarzenia muszą być wizualizowany i jest dostępny w niektórych formacie, aby zezwolić na potrzeby analizy i wyświetlania. Istnieje kilka platformy do analizy i wizualizacji danych monitorowania i diagnostyki. Firma Microsoft zaleca [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) i [usługi Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) ponieważ ich bezproblemową integrację z usługi Service Fabric.

Można również użyć [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) do monitorowania wielu zasobów platformy Azure, na których bazuje klaster usługi Service Fabric.

Strażnika jest oddzielną usługą, którą można obejrzeć kondycji i obciążenia usług i kondycji raportu dla każdego elementu w hierarchii modelu kondycji. Za pomocą strażnika może zapobiec błędów, które nie są wykrywane na podstawie widoku jednej usługi. 

Watchdogs jest również dobrym miejscem do śledzenia hostowanie kodu, który wykonuje czynności zaradczych bez interakcji z użytkownikiem. Przykład czyści pliki dziennika w magazynie w określonych odstępach czasu. Można znaleźć przykład implementacji usługi strażnika na [przykładowe strażnika usługi Azure Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Bezpieczna komunikacja przy użyciu certyfikatów
Certyfikaty pomóc w bezpiecznej komunikacji między różnymi węzłami klastra autonomicznego Windows. Za pomocą certyfikatów X.509, mogą również uwierzytelniać klientów łączących się z tym klastrem. Daje to gwarancję, że tylko autoryzowani użytkownicy mogą uzyskać dostęp do klastra. Firma Microsoft zaleca, aby włączyć certyfikatu dla klastra podczas jego tworzenia.

Cyfrowe certyfikaty X.509 są często używane do uwierzytelniania klientów i serwerów. Są one także używane do szyfrowania i cyfrowego podpisywania wiadomości.

Poniższa tabela zawiera listę certyfikatów, które należy na konfigurację klastra:

|Ustawienie informacji dotyczących certyfikatów |Opis|
|-------------------------------|-----------|
|ClusterCertificate|    Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć dwóch certyfikatów klastra: certyfikatu podstawowego i pomocniczego do uaktualnienia.|
|ServerCertificate| Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. Można użyć dwóch certyfikatów serwera: certyfikatu podstawowego i pomocniczego do uaktualnienia.|
|ClientCertificateThumbprints|  Jest to zbiór certyfikaty do zainstalowania na uwierzytelnionych klientów.|
|ClientCertificateCommonNames|  Jest to nazwa pospolita pierwszy certyfikat klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca wystawcy certyfikatu.|
|ReverseProxyCertificate|   Jest to opcjonalne certyfikat, który można określić, aby zabezpieczyć swoje [zwrotny serwer proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Aby uzyskać więcej informacji na temat zabezpieczania certyfikatów, zobacz [Zabezpieczanie klastra autonomicznego w Windows przy użyciu certyfikatów X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Zrozumienie kontroli dostępu opartej na rolach
Określasz role klienta administratora i użytkownika w czasie tworzenia klastra, podając osobne tożsamości (w tym certyfikaty) dla każdego. Aby uzyskać więcej informacji na temat domyślnego ustawienia kontroli dostępu oraz sposobu zmiany ustawień domyślnych, zobacz [kontroli dostępu opartej na rolach dla klientów usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Zabezpieczanie klastrów autonomicznych przy użyciu zabezpieczeń Windows
Aby zapobiec nieautoryzowanemu dostępowi do klastra usługi Service Fabric, należy zabezpieczyć klaster. Bezpieczeństwo jest szczególnie ważne, gdy klaster działa obciążeń produkcyjnych. Węzeł węzeł i węzeł klienta zabezpieczeń można skonfigurować za pomocą zabezpieczeń Windows w pliku ClusterConfig.JSON.

Gdy Usługa Service Fabric musi działać w ramach gMSA, konfigurowanie zabezpieczeń między węzłami, ustawiając [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Aby utworzyć relacje zaufania między węzłami, należy to zrobić pamiętać od siebie.

Jeśli chcesz użyć grupy na komputerze w domenie usługi Active Directory, możesz skonfigurować zabezpieczeń między węzłami, ustawiając ClusterIdentity. Aby uzyskać więcej informacji, zobacz [Utwórz grupę maszyny w usłudze Active Directory](https://msdn.microsoft.com/library/aa545347).

Węzeł klienta zabezpieczeń można skonfigurować za pomocą ClientIdentities. Należy skonfigurować klaster, aby rozpoznać tożsamości klienta, które można ufać. Można ustanowić relacji zaufania na dwa sposoby:

-   Określ użytkowników grupy domeny, którymi mogą nawiązywać połączenie.
-   Określ użytkowników węzeł domeny, którymi mogą nawiązywać połączenie.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurowanie zabezpieczeń aplikacji w usłudze Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Zarządzanie wpisami tajnymi w aplikacji usługi Service Fabric
Wpisy tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu.

Możesz użyć [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do zarządzania kluczami i wpisami tajnymi. Jednak użycie kluczy tajnych w aplikacji nie jest zależny od platformy w chmurze określonych. Można wdrożyć aplikacji do klastra, które jest hostowane w dowolnym miejscu. Istnieją cztery główne kroki, w tym przepływie:

1.  Uzyskaj certyfikat Szyfrowanie danych.
2.  Zainstaluj certyfikat w klastrze.
3.  Szyfrowanie klucza tajnego wartości podczas wdrażania aplikacji za pomocą certyfikatu i wstawić je do pliku konfiguracji Settings.xml usługi.
4.  Odczyt zaszyfrowanych wartości poza Settings.xml celu odszyfrowuje je za pomocą tego samego certyfikatu szyfrowania.

Aby uzyskać więcej informacji, zobacz [zarządzania wpisami tajnymi w aplikacji usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Podczas konfigurowania zasad zabezpieczeń dla aplikacji
Za pomocą zabezpieczeń usługi Azure Service Fabric, możesz pomóc bezpieczne aplikacje, które są uruchomione w klastrze, w ramach różnych kont użytkowników. Zabezpieczenia usługi Service Fabric pomaga również w zabezpieczenia zasobów korzystających z aplikacji w czasie wdrażania na kontach użytkowników — na przykład, pliki, katalogi i certyfikatów. Dzięki temu uruchomionych aplikacji, nawet w środowisku współdzielonym hostowanej bardziej bezpieczne.

Zadania dotyczące konfigurowania zasad zabezpieczeń obejmują:

-   Konfigurowanie zasad dla punktu wejścia usługi Instalatora
-   Uruchamianie poleceń programu PowerShell z punktu wejścia Instalatora
-   Używanie konsoli przekierowania dla debugowania lokalnego
-   Konfigurowanie zasad dla usługi pakiety kodu
-   Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS

## <a name="secure-communication-for-services"></a>Bezpieczna komunikacja usług
Zabezpieczeń jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji usług Reliable Services zawiera kilka wbudowanych komunikacji stosy i narzędzia, których można użyć w celu zwiększenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [bezpieczna komunikacja usług zdalnych usług dla usługi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać ogólne informacje na temat zabezpieczeń klastra, zobacz [tworzenia klastra usługi Service Fabric za pomocą usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) i [tworzenia klastra usługi Service Fabric za pomocą witryny Azure portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Aby dowiedzieć się więcej na temat zabezpieczeń klastra w usłudze Service Fabric, zobacz [scenariusze zabezpieczeń klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
