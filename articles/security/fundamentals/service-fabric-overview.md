---
title: Omówienie zabezpieczeń usługi Azure Service Fabric | Microsoft Docs
description: Ten artykuł zawiera omówienie zabezpieczeń usługi Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 75888d9da0d8a6e76e787b7d3867e94eefff337e
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726571"
---
# <a name="azure-service-fabric-security-overview"></a>Omówienie zabezpieczeń usługi Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Service Fabric rozwiązuje problemy związane z tworzeniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i Administratorzy mogą uniknąć złożonych problemów z infrastrukturą i skupić się na wdrażaniu wymagających obciążeń o krytycznym znaczeniu, które są skalowalne i niezawodne.

Ten artykuł zawiera omówienie zagadnień związanych z zabezpieczeniami Service Fabric wdrożenia.

## <a name="secure-your-cluster"></a>Zabezpieczanie klastra
Usługa Azure Service Fabric organizuje usługi w klastrze maszyn. Klastry muszą być zabezpieczone, aby uniemożliwić nieautoryzowanym użytkownikom łączenie się z nimi, szczególnie w przypadku uruchamiania obciążeń produkcyjnych. Chociaż istnieje możliwość utworzenia niezabezpieczonego klastra, może to pozwolić anonimowym użytkownikom na łączenie się z klastrem (jeśli ujawnia punkty końcowe zarządzania do publicznej sieci Internet).

W przypadku klastrów, na których działa autonomiczna lub na platformie Azure, dwa scenariusze, które należy wziąć pod uwagę, to zabezpieczenia między węzłami i zabezpieczenia klient-węzeł. Aby zaimplementować te scenariusze, można użyć różnych technologii.

### <a name="node-to-node-security"></a>Zabezpieczenia między węzłami
Zabezpieczenia między węzłami dotyczą komunikacji między maszynami wirtualnymi lub maszynami w klastrze. W przypadku zabezpieczeń między węzłami tylko komputery autoryzowane do przyłączenia do klastra mogą uczestniczyć w aplikacjach i usługach w klastrze.

Klastry działające na platformie Azure lub w klastrach autonomicznych, które działają w systemie Windows, mogą używać [zabezpieczeń certyfikatów](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczeń systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx) na komputerach z systemem Windows Server.

#### <a name="node-to-node-certificate-security"></a>Zabezpieczenia certyfikatów między węzłami

Service Fabric używa certyfikatów serwera X. 509, które są określane podczas tworzenia klastra. Aby uzyskać szybki przegląd informacji o tych certyfikatach i sposobach ich pozyskiwania lub tworzenia, zobacz [Praca z certyfikatami](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Zabezpieczenia certyfikatów konfiguruje się podczas tworzenia klastra za pomocą szablonów Azure Portal, Azure Resource Manager lub autonomicznego szablonu JSON. Można określić certyfikat podstawowy i opcjonalny certyfikat pomocniczy, który jest używany na potrzeby przerzucania certyfikatów. Określone certyfikaty podstawowe i pomocnicze powinny się różnić od klienta administratora i certyfikatów klienta tylko do odczytu określonych dla [zabezpieczeń klient-węzeł](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Zabezpieczenia między klientem a węzłem
Zabezpieczenia klient-węzeł można skonfigurować przy użyciu tożsamości klienta. Aby ustanowić relację zaufania między klientem a klastrem, należy skonfigurować klaster, aby wiedzieć, które tożsamości klientów mogą ufać.

Service Fabric obsługuje dwa typy kontroli dostępu dla klientów, którzy są połączeni z klastrem Service Fabric:

-   **Administrator**: Pełny dostęp do możliwości zarządzania, w tym możliwości odczytu i zapisu.
-   **Użytkownik**: Dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania) i możliwość rozpoznawania aplikacji i usług.

Za pomocą kontroli dostępu Administratorzy klastrów mogą ograniczyć dostęp do niektórych typów operacji klastra. To sprawia, że klaster jest bezpieczniejszy.

#### <a name="client-to-node-certificate-security"></a>Zabezpieczenia certyfikatów między klientami a węzłami

Zabezpieczenia certyfikatu klient-węzeł konfiguruje się podczas tworzenia klastra za pomocą Azure Portal, Menedżer zasobów szablonów lub autonomicznego szablonu JSON. Należy określić certyfikat klienta administratora i/lub certyfikat klienta użytkownika. Upewnij się, że te certyfikaty różnią się od podstawowych i pomocniczych certyfikatów określonych dla zabezpieczeń między węzłami.

Klienci, którzy łączą się z klastrem przy użyciu certyfikatu administratora, mają pełny dostęp do możliwości zarządzania. Klienci łączący się z klastrem przy użyciu certyfikatu klienta użytkownika tylko do odczytu mają dostęp tylko do odczytu do funkcji zarządzania. Innymi słowy, te certyfikaty są używane na potrzeby kontroli dostępu opartej na rolach (RBAC).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze, zobacz [Konfigurowanie klastra przy użyciu szablonu Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Zabezpieczenia Azure Active Directory klienta-węzła

Klastry działające na platformie Azure mogą również zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu Azure Active Directory (Azure AD). Aby uzyskać informacje na temat sposobu tworzenia niezbędnych artefaktów Azure Active Directory, sposobu ich wypełniania podczas tworzenia klastra i łączenia się z tymi klastrami, zobacz [Konfigurowanie klastra przy użyciu szablonu Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. Istnieją aplikacje z interfejsem użytkownika logowania opartego na sieci Web oraz aplikacje korzystające z natywnego środowiska klienta.

Klaster Service Fabric oferuje kilka punktów wejścia do funkcji zarządzania, w tym Service Fabric Explorer sieci Web i program Visual Studio. W związku z tym utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną.

W przypadku klastrów platformy Azure zalecamy użycie zabezpieczeń usługi Azure AD do uwierzytelniania klientów i certyfikatów w celu zabezpieczenia węzłów między węzłami.

W przypadku autonomicznych klastrów systemu Windows Server z systemami Windows Server 2012 R2 i Active Directory zalecamy używanie zabezpieczeń systemu Windows z kontami usług zarządzanymi przez grupę (kont gMSA). W przeciwnym razie Użyj zabezpieczeń systemu Windows z kontami systemu Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Omówienie monitorowania i diagnostyki w Service Fabric
[Monitorowanie i Diagnostyka](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) mają kluczowe znaczenie dla opracowywania, testowania i wdrażania aplikacji i usług w dowolnym środowisku. Service Fabric rozwiązania działają najlepiej podczas implementowania monitorowania i diagnostyki, aby upewnić się, że aplikacje i usługi działają zgodnie z oczekiwaniami w lokalnym środowisku programistycznym lub w produkcji.

Z punktu widzenia zabezpieczeń główne cele monitorowania i diagnostyki są następujące:

-   Wykrywanie i diagnozowanie problemów ze sprzętem i infrastrukturą, które mogą być spowodowane zdarzeniem zabezpieczeń.
-   Wykrywaj problemy z oprogramowaniem i aplikacjami, które mogą być wskaźnikami naruszenia (IoC).
-   Informacje o sposobie użycia zasobów w celu zapobiegania przypadkowemu odmowie usługi.

Przepływ pracy monitorowania i diagnostyki składa się z trzech kroków:

1.  **Generowanie zdarzeń**: Generowanie zdarzeń obejmuje zdarzenia (dzienniki, ślady, zdarzenia niestandardowe) zarówno na poziomie infrastruktury (klastra), jak i na poziomie aplikacji/usług. Przeczytaj więcej na temat [zdarzeń na poziomie infrastruktury](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) i [zdarzeń na poziomie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) , aby dowiedzieć się, jakie dane i jak dodać dalsze Instrumentacja.

2.  **Agregacja zdarzeń**: Wygenerowane zdarzenia należy zebrać i agregować, aby można było je wyświetlić. Zwykle zalecamy używanie [Diagnostyka Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (podobnie jak w przypadku zbierania dzienników opartych na agentach) lub [użyciu struktury eventflow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (zbieranie dzienników w procesie).

3.  **Analiza**: Zdarzenia muszą być wizualizowane i dostępne w niektórych formatach, aby umożliwić analizę i wyświetlanie. Istnieje kilka platform do analizy i wizualizacji danych monitorowania i diagnostyki. Zalecamy [Azure monitor dzienników](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) i [Application Insights platformy Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) , ponieważ są one dobrze zintegrowane z Service Fabric.

Za pomocą [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) można monitorować wiele zasobów platformy Azure, na których jest tworzony klaster Service Fabric.

Licznik alarm jest oddzielną usługą, która może oglądać kondycję i ładować w ramach usług oraz zgłaszać kondycję dla wszystkich elementów w hierarchii modelu kondycji. Użycie licznika Alarm może pomóc zapobiec błędom, które nie zostaną wykryte w oparciu o Widok jednej usługi. 

Licznik alarmy jest również dobrym miejscem do kodu hosta, który wykonuje akcje zaradcze bez interakcji z użytkownikiem. Przykładem jest czyszczenie plików dziennika w magazynie w określonych przedziałach czasowych. Przykładową implementację usługi strażnika można znaleźć na [platformie Azure Service Fabric licznik alarmowy](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Bezpieczna komunikacja przy użyciu certyfikatów
Certyfikaty pomagają w zabezpieczaniu komunikacji między różnymi węzłami autonomicznego klastra systemu Windows. Za pomocą certyfikatów X. 509 można także uwierzytelniać klientów łączących się z tym klastrem. Dzięki temu tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra. Zalecamy włączenie certyfikatu w klastrze podczas jego tworzenia.

Certyfikaty cyfrowe X. 509 są często używane do uwierzytelniania klientów i serwerów. Są one również używane do szyfrowania i cyfrowego podpisywania wiadomości.

W poniższej tabeli wymieniono certyfikaty, które są potrzebne w konfiguracji klastra:

|Ustawienie informacji o certyfikacie |Opis|
|-------------------------------|-----------|
|ClusterCertificate|    Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć dwóch certyfikatów klastra: certyfikatu podstawowego i pomocniczego do uaktualnienia.|
|ServerCertificate| Ten certyfikat jest prezentowany klientowi podczas próby nawiązania połączenia z tym klastrem. Można użyć dwóch certyfikatów serwera: certyfikatu podstawowego i pomocniczego do uaktualnienia.|
|ClientCertificateThumbprints|  Jest to zestaw certyfikatów do zainstalowania na uwierzytelnionych klientach.|
|ClientCertificateCommonNames|  Jest to nazwa pospolita pierwszego certyfikatu klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odciskiem palca dla wystawcy tego certyfikatu.|
|ReverseProxyCertificate|   Jest to opcjonalny certyfikat, który można określić w celu zabezpieczenia [zwrotnego serwera proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Aby uzyskać więcej informacji na temat zabezpieczania certyfikatów, zobacz [Zabezpieczanie klastra autonomicznego w systemie Windows za pomocą certyfikatów X. 509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Informacje na temat kontroli dostępu opartej na rolach
Użytkownik określa role administratorów i użytkowników w momencie tworzenia klastra, dostarczając osobne tożsamości (w tym certyfikaty) dla każdego z nich. Aby uzyskać więcej informacji na temat domyślnych ustawień kontroli dostępu i sposobu zmiany ustawień domyślnych, zobacz [Kontrola dostępu oparta na rolach dla klientów Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Zabezpieczanie klastrów autonomicznych przy użyciu zabezpieczeń systemu Windows
Aby zapobiec nieautoryzowanemu dostępowi do klastra Service Fabric, należy zabezpieczyć klaster. Zabezpieczenia są szczególnie ważne w przypadku uruchamiania przez klaster obciążeń produkcyjnych. W pliku ClusterConfig. JSON można skonfigurować zabezpieczenia węzłów między węzłami i klient-węzeł przy użyciu zabezpieczeń systemu Windows.

Gdy Service Fabric musi działać w ramach gMSA, należy skonfigurować zabezpieczenia typu węzeł-węzeł, ustawiając [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Aby utworzyć relacje zaufania między węzłami, należy się z nimi zapoznać.

Jeśli chcesz użyć grupy maszyn w domenie Active Directory, skonfiguruj zabezpieczenia między węzłami, ustawiając ClusterIdentity. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy maszyn w Active Directory](https://msdn.microsoft.com/library/aa545347).

Należy skonfigurować zabezpieczenia między węzłami przy użyciu ClientIdentities. Należy skonfigurować klaster, aby rozpoznawał, które tożsamości klientów mogą ufać. Zaufanie można ustanowić na dwa sposoby:

-   Określ grupę domen, którą mogą nawiązywać połączenia.
-   Określ węzeł domeny Użytkownicy, którzy mogą nawiązywać połączenia.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurowanie zabezpieczeń aplikacji w Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Zarządzanie wpisami tajnymi w aplikacjach Service Fabric
Wpisy tajne mogą być dowolnymi informacjami poufnymi, takimi jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu.

Za pomocą [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) można zarządzać kluczami i wpisami tajnymi. Jednak korzystanie z wpisów tajnych w aplikacji nie zależy od określonej platformy w chmurze. Aplikacje można wdrażać w klastrze hostowanym w dowolnym miejscu. W tym przepływie istnieją cztery główne kroki:

1.  Pobierz certyfikat szyfrowania danych.
2.  Zainstaluj certyfikat w klastrze.
3.  Szyfruj wartości tajne podczas wdrażania aplikacji z certyfikatem i wstrzyknąć je do pliku konfiguracyjnego XML ustawień usługi.
4.  Odczytaj zaszyfrowane wartości z pliku Settings. xml przez odszyfrowanie ich przy użyciu tego samego certyfikatu szyfrowania.

Aby uzyskać więcej informacji, zobacz Zarządzanie wpisami [tajnymi w aplikacjach Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Konfigurowanie zasad zabezpieczeń dla aplikacji
Korzystając z usługi Azure Service Fabric Security, można zabezpieczyć aplikacje działające w klastrze pod różnymi kontami użytkowników. Zabezpieczenia Service Fabric zabezpieczają także zasoby używane przez aplikacje w czasie wdrażania w ramach kont użytkowników — na przykład pliki, katalogi i certyfikaty. Dzięki temu uruchomione aplikacje, nawet w udostępnianym środowisku hostowanym, są bezpieczniejsze.

Zadania związane z konfigurowaniem zasad zabezpieczeń obejmują:

-   Konfigurowanie zasad dla punktu wejścia Instalatora usługi
-   Uruchamianie poleceń programu PowerShell z punktu wejścia Instalatora
-   Używanie przekierowania konsoli do debugowania lokalnego
-   Konfigurowanie zasad dla pakietów kodu usługi
-   Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS

## <a name="secure-communication-for-services"></a>Bezpieczna komunikacja dla usług
Bezpieczeństwo jest jednym z najważniejszych aspektów komunikacji. Platforma aplikacji Reliable Services zawiera kilka wstępnie utworzonych stosów i narzędzi do komunikacji, których można użyć w celu zwiększenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [Bezpieczna komunikacja zdalna usługi dla usługi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Kolejne kroki
- Informacje o pojęciach dotyczących zabezpieczeń klastra znajdują się w temacie [Tworzenie klastra Service Fabric przy użyciu Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) i [Tworzenie klastra Service Fabric przy użyciu Azure Portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Aby dowiedzieć się więcej o zabezpieczeniach klastra w Service Fabric, zobacz [Service Fabric scenariusze zabezpieczeń klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
