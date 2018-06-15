---
title: Omówienie zabezpieczeń usługi Azure sieci szkieletowej | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie zabezpieczeń usługi Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 19e4e55c4bd63e5d7a9ef6ea3b0bf6ae63f929d5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895667"
---
# <a name="azure-service-fabric-security-overview"></a>Omówienie zabezpieczeń usługi Azure Service Fabric
[Sieć szkieletowa usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) to platforma systemów rozproszonych, która ułatwia pakietów, wdrażanie i zarządzanie mikrousług skalowalne i niezawodne. Sieć szkieletowa usług adresów istotnych wyzwań rozwijania i zarządzanie aplikacjami w chmurze. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

Azure Service Fabric zabezpieczeń ten artykuł skupia się wokół następujących obszarów:

-   Zabezpieczanie klastra
-   Opis monitorowania i diagnostyki
-   Tworzenie środowisk bezpieczniejsze przy użyciu certyfikatów
-   Za pomocą kontroli dostępu opartej na rolach (RBAC)
-   Zabezpieczanie klastrów za pomocą zabezpieczeń systemu Windows
-   Konfigurowanie zabezpieczeń aplikacji w sieci szkieletowej usług
-   Zabezpieczenia komunikacji dla usług w sieci szkieletowej usług Azure 

## <a name="secure-your-cluster"></a>Zabezpieczanie klastra
Sieć szkieletowa usług Azure organizuje usługi w klastrze maszyn. Aby zapobiec nieautoryzowanemu połączyć się z nimi, szczególnie w przypadku, gdy są one uruchomione obciążeń produkcyjnych musi być zabezpieczona klastrów. Chociaż można utworzyć klaster niezabezpieczoną, może to umożliwić użytkownikom anonimowym się z nim połączyć (jeśli takie punkty końcowe zarządzania do publicznego Internetu).

Ta sekcja zawiera omówienie scenariuszy zabezpieczeń dla klastrów, które działają jako produkt autonomiczny lub na platformie Azure. Omówiono także różne technologie, które są używane do wdrożenia tych scenariuszy. Dostępne są następujące scenariusze zabezpieczeń klastra:

-   Zabezpieczenia węzła do węzła
-   Węzeł klienta zabezpieczeń

### <a name="node-to-node-security"></a>Zabezpieczenia węzła do węzła
Zabezpieczenia węzła do węzła zabezpiecza komunikację między maszyn wirtualnych lub maszyn w klastrze. Z węzła do węzła zabezpieczeń tylko te komputery, które są autoryzowane do przyłączenia się do klastra mogą uczestniczyć w hosting aplikacji i usług w klastrze.

Klastry działające w klastrach Azure lub autonomiczne, które są uruchomione w systemie Windows można użyć dowolnego [certyfikatu zabezpieczeń](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczenia systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx) dla komputerów z systemem Windows Server.

**Zrozumienie zabezpieczeń certyfikatu węzła do węzła**

Sieć szkieletowa usług korzysta z certyfikatów X.509 określić podczas tworzenia klastra. Szybkie omówienie certyfikaty te są i jak uzyskać lub je utworzyć, zobacz [Praca z certyfikatami](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Certyfikat zabezpieczeń można skonfigurować podczas tworzenia klastra, albo za pośrednictwem portalu Azure, szablony usługi Azure Resource Manager lub szablonu JSON w autonomicznych. Można określić podstawowy certyfikat i opcjonalne dodatkowej certyfikat, który jest używany do najazdy certyfikatu. Certyfikatów głównych i dodatkowych, możesz określić powinien być inny niż administrator klienta i certyfikatów klienta tylko do odczytu, które określisz dla [zabezpieczeń węzeł klient](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Węzeł klienta zabezpieczeń
Konfigurowanie zabezpieczeń w węźle klienta przy użyciu tożsamości klienta. Aby ustanowić zaufanie między klientem klastrem, należy skonfigurować klaster, aby wiedzieć, który klient tożsamości, które on zaufany. Można to zrobić na dwa sposoby:

-   Określ użytkowników grupy domeny, które mogą nawiązywać połączenia. 
-   Określ użytkowników węzeł domeny, które mogą nawiązywać połączenia.

Sieć szkieletowa usług obsługuje dwa typy kontroli różny dostęp dla klientów, które są podłączone do klastra usługi sieć szkieletowa usług:

-   Administrator
-   Użytkownik

Przy użyciu kontroli dostępu, klastra, Administratorzy mogą ograniczyć dostęp do niektórych typów operacji klastra. Dzięki temu klaster są bardziej bezpieczne.

 Administratorzy mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług.

**Zrozumienie zabezpieczeń certyfikat klienta do węzła**

Węzeł Klient certyfikatu zabezpieczeń można skonfigurować podczas tworzenia klastra przy użyciu portalu Azure, szablonów usługi Resource Manager lub szablonu JSON w autonomicznej. Należy określić certyfikat klienta administratora i/lub certyfikatu klienta użytkownika. 

Administrator klientów i użytkowników certyfikaty klienta, które określisz powinien być inny niż certyfikatów głównych i dodatkowych, określonych dla zabezpieczeń węzła do węzła.

Klienci łączący się do klastra przy użyciu certyfikatu admin mają pełny dostęp do możliwości zarządzania. Klienci łączący się z klastrem przy użyciu certyfikatu klienta użytkownika tylko do odczytu mają dostęp tylko do odczytu do możliwości zarządzania. Innymi słowy te certyfikaty są używane do RBAC.

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń w klastrze, zobacz [Konfigurowanie klastra za pomocą szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Zrozumienie zabezpieczeń usługi Azure Active Directory węzeł klienta na platformie Azure**

Klastry działające na platformie Azure można również bezpieczny dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (Azure AD). Aby uzyskać informacji na temat sposobu tworzenia artefakty niezbędne usługi Azure Active Directory, sposobu wypełniać ich podczas tworzenia klastra oraz sposób nawiązywania połączenia z tych klastrów, zobacz [Konfigurowanie klastra za pomocą szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usługi Azure AD umożliwia organizacjom (nazywane dzierżawcy), aby zarządzać dostępem użytkowników do aplikacji. Brak aplikacji z interfejsem sieci web logowania użytkownika i aplikacji przy użyciu środowiska macierzystego klienta.

Klaster sieci szkieletowej usług oferuje kilka punktów wejścia do jego funkcjonalność zarządzania, w tym Service Fabric Explorer i Visual Studio opartych na sieci web. W związku z tym utworzyć dwie aplikacje usługi Azure AD do kontrolowania dostępu do klastra: jednej aplikacji sieci web i jednej aplikacji natywnej.

W przypadku klastrów platformy Azure zalecane jest użycie zabezpieczeń usługi Azure AD na potrzeby uwierzytelniania klientów i certyfikatów dla zabezpieczeń węzła do węzła.

W przypadku autonomicznych klastrów systemu Windows Server z systemu Windows Server 2012 R2 i usługi Active Directory zalecane jest użycie zabezpieczeń systemu Windows z kontami zarządzane przez grupę.  W przeciwnym razie użyj zabezpieczenia systemu Windows przy użyciu kont systemu Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Informacje o monitorowaniu i diagnostyki w sieci szkieletowej usług Azure
[Monitorowania i diagnostyki](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) są krytyczne dla programowania, testowania i wdrażania aplikacji i usług w każdym środowisku. Rozwiązania sieci szkieletowej usług działają najlepiej, gdy wdrożenie monitorowania i diagnostyki, aby upewnić się, że aplikacje i usługi działać zgodnie z oczekiwaniami w środowisku projektowym lokalnej lub w środowisku produkcyjnym.

Z punktu widzenia zabezpieczeń są główne cele monitorowania i diagnostyki:

-   Wykrywanie i diagnozowanie problemów sprzętu i infrastruktury, które mogą być spowodowane przez zdarzeń zabezpieczeń.
-   Wykrywanie problemów oprogramowania i aplikacji, które może być wskaźnikiem naruszenia (Inwersja kontroli).
-   Zrozumienie zużycie zasobów, aby zapobiec niepotrzebnemu "odmowa usługi".

Ogólny przepływ pracy monitorowania i diagnostyki obejmuje trzy kroki:

-   **Generowanie zdarzeń:** generowania zdarzeń zawiera zdarzenia (dzienników, śledzenie zdarzeń niestandardowych) na infrastruktury (klastra) i na poziomie aplikacji/usługi. Przeczytaj więcej na temat [zdarzenia na poziomie infrastruktury](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) i [zdarzenia na poziomie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) zrozumieć, jaka jest dostępna oraz sposób zwiększenia instrumentacji.

-   **Agregacja zdarzeń:** Generated zdarzeń muszą być zbierane i agregować przed mogą być wyświetlane. Zazwyczaj zalecane jest używanie [diagnostyki Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (podobny do zbierania dzienników opartej o agentów) lub [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (w procesie zbierania dzienników).

-   **Analiza:** zdarzeń muszą być wizualizowanego i jest dostępna w niektórych formacie zezwalająca na potrzeby analizy i wyświetlania. Istnieje kilka platformy analizy i wizualizacji danych monitorowania i diagnostyki. Są dwa, które są zalecane [analizy dzienników](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) i [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) z powodu ich dobrej integracji z sieci szkieletowej usług.

Można również użyć [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) do monitorowania wielu zasobów platformy Azure, na których jest zbudowany klastra sieci szkieletowej usług.

Programu alarmowego to oddzielne usługa, która może oglądać kondycji, obciążenie usługi oraz generowania raportów kondycji dla wszystkich elementów w hierarchii modelu kondycji. Za pomocą programu alarmowego mogą pomagać w zapobieganiu błędy, których nie można wykryć na podstawie widoku jednej usługi. 

Watchdogs są również dobrym miejscem do hosta kod, który wykonuje czynności zaradczych bez interakcji z użytkownikiem (na przykład czyszczenie plików dziennika w magazynie, co pewien czas). Można znaleźć implementacji usługi programu alarmowego próbki, przy [próbki programu alarmowego sieć szkieletowa usług Azure](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Zrozumienie sposobu zabezpieczania komunikacji przy użyciu certyfikatów
Certyfikaty pomóc zabezpieczają komunikację między różnych węzłów w klastrze Windows autonomicznych. Za pomocą certyfikatów X.509, mogą również uwierzytelniać klientów łączących się z tym klastrem. Daje to pewność, że tylko autoryzowani użytkownicy mogą uzyskać dostęp do klastra. Zaleca się włączenie certyfikatu w klastrze podczas jej tworzenia.

### <a name="x509-certificates-and-service-fabric"></a>Certyfikaty X.509 i sieci szkieletowej usług
Certyfikaty cyfrowe X.509 często są używane do uwierzytelniania klientów i serwerów. Są one również używane do szyfrowania i cyfrowego podpisywania wiadomości.

W poniższej tabeli wymieniono certyfikaty, które należy na konfigurację klastra:

|Ustawienie informacji dotyczących certyfikatów |Opis|
|-------------------------------|-----------|
|ClusterCertificate|    Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć dwóch różnych certyfikatów: certyfikat podstawowego i pomocniczego do uaktualnienia.|
|ServerCertificate| Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. Można użyć dwóch różnych certyfikatów: certyfikat podstawowego i pomocniczego do uaktualnienia.|
|ClientCertificateThumbprints|  Jest to zestaw certyfikaty do zainstalowania na klientach uwierzytelniony.|
|ClientCertificateCommonNames|  Jest to nazwa pospolita pierwszy certyfikat klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca wystawcy certyfikatu.|
|ReverseProxyCertificate|   Jest to opcjonalny certyfikat, który może być określony w celu zabezpieczenia sieci [odwrotny serwer proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Aby uzyskać więcej informacji na temat zabezpieczania certyfikatów, zobacz [Secure autonomiczny klastra w systemie Windows za pomocą certyfikatów X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Zrozumienie kontroli dostępu opartej na rolach
Kontrola dostępu umożliwia administratora klastrów, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, dzięki czemu bezpieczniejsze klastra. Obsługiwane są dwa typy kontroli dostępu różnych dla klientów łączących się z klastrem: 

- Rola administratora
- Rola użytkownika

Administratorzy mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług.

Określonej klienta role administratora i użytkownika w czasie tworzenia klastra przez podanie oddzielne tożsamości (takich jak certyfikaty) dla każdego. Aby uzyskać więcej informacji na temat domyślne ustawienia kontroli dostępu oraz sposobu zmiany ustawień domyślnych, zobacz [opartej na rolach kontroli dostępu dla klientów usługi sieć szkieletowa](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Zabezpieczanie klastra autonomiczny przy użyciu zabezpieczeń systemu Windows
Aby uniemożliwić nieautoryzowany dostęp do klastra usługi sieć szkieletowa, należy zabezpieczyć klastra. Zabezpieczeń jest szczególnie ważne, jeśli klaster uruchamia obciążeń produkcyjnych. Przedstawiono sposób konfigurowania zabezpieczeń między węzłami i węzeł klienta przy użyciu zabezpieczeń systemu Windows w pliku ClusterConfig.JSON pliku.

**Konfigurowanie zabezpieczeń systemu Windows przy użyciu usługi zarządzane przez grupę**

Gdy sieć szkieletowa usług musi działać w ramach usługi zarządzane przez grupę, konfigurowanie zabezpieczeń węzła do węzła ustawiając [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Do tworzenia relacji zaufania między węzłami, ich należy pamiętać o sobie nawzajem.

Konfigurowanie zabezpieczeń w węźle klienta przy użyciu ClientIdentities. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby rozpoznać tożsamości klienta, które on zaufany.

**Konfigurowanie zabezpieczeń systemu Windows przy użyciu grupy na komputerze**

Jeśli chcesz użyć grupy na komputerze w domenie usługi Active Directory, należy skonfigurować zabezpieczenia węzła do węzła ustawiając ClusterIdentity. Aby uzyskać więcej informacji, zobacz [Utwórz grupę maszyny w usłudze Active Directory](https://msdn.microsoft.com/library/aa545347).

Konfigurowanie zabezpieczeń w węźle klienta przy użyciu ClientIdentities. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby rozpoznać tożsamości klienta, które można ufać klastra. Można ustanowić relacji zaufania na dwa sposoby:

-   Określ użytkowników grupy domeny, które mogą nawiązywać połączenia.
-   Określ użytkowników węzeł domeny, które mogą nawiązywać połączenia.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurowanie zabezpieczeń aplikacji w sieci szkieletowej usług
### <a name="manage-secrets-in-service-fabric-applications"></a>Zarządzanie kluczy tajnych w aplikacji sieci szkieletowej usług
Ta metoda ułatwia zarządzanie kluczy tajnych w aplikacji sieci szkieletowej usług. Klucze tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu.

Ta metoda używa [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do zarządzania kluczy i kluczy tajnych. Jednak przy użyciu kluczy tajnych w aplikacji jest funkcja platformy chmury. Oznacza to, że aplikacje można wdrażać do klastra, który znajduje się w dowolnym miejscu. W tym przepływie istnieją cztery główne kroki:

-   Uzyskaj certyfikat Szyfrowanie danych.
-   Zainstaluj certyfikat w klastrze.
-   Szyfrowanie tajny wartości podczas wdrażania aplikacji przy użyciu certyfikatu i wstawić je do pliku konfiguracji Settings.xml usługi.
-   Przeczytaj zaszyfrowane wartości poza Settings.xml w tym celu odszyfrowuje je z tego samego certyfikatu szyfrowanie.

>[!NOTE]
>Dowiedz się więcej o [Zarządzanie kluczy tajnych w aplikacji usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Konfigurowanie zasad zabezpieczeń aplikacji
Przy użyciu zabezpieczeń sieć szkieletowa usług Azure, możesz pomóc zabezpieczenia aplikacji, które są uruchomione w klastrze, w obszarze konta innego użytkownika. Usługa sieci szkieletowej zabezpieczeń pomaga również w zabezpieczenia zasobów, które są używane przez aplikacje w czasie wdrażania na kontach użytkowników — na przykład, plików, katalogów i certyfikatów. Dzięki temu uruchamianie aplikacji, nawet w środowisku hostowanej udostępnionego bardziej bezpieczne.

Kroki obejmują:

-   Konfigurowanie zasad dla punktu wejścia instalacji usługi.
-   Uruchamianie poleceń programu PowerShell z punktu wejścia instalacji.
-   Przy użyciu konsoli dla debugowania lokalnego.
-   Konfigurowanie zasad dla pakietów kodu usługi.
-   Przypisywanie dostępu zasady zabezpieczeń dla punktów końcowych HTTP i HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Bezpiecznej komunikacji dla usług w sieci szkieletowej usług Azure zabezpieczeń
Zabezpieczeń jest jednym z najważniejszych aspektów komunikacji. Struktura aplikacji niezawodne usługi zapewnia kilka stosy wbudowane komunikacji i narzędzi, których można użyć w celu poprawy bezpieczeństwa.

-   [Zabezpieczanie usługi podczas korzystania z komunikacji zdalnej usługi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Zabezpieczanie usługi podczas korzystania z stosu komunikacji usługi WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać ogólne informacje o zabezpieczeniach klastra, zobacz [tworzenia klastra usługi sieć szkieletowa usług za pomocą usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) i [portalu Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Aby dowiedzieć się więcej na temat zabezpieczeń klastra w sieci szkieletowej usług, zobacz [zabezpieczeń klastra sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
