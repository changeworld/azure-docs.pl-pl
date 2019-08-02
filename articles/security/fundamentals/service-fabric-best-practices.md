---
title: Najlepsze rozwiązania dotyczące usługi Azure Service Fabric Security | Microsoft Docs
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących usługi Azure Service Fabric Security.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 7e7d57b30734d8cfdff42b70dd38b5afa41a9ea9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726621"
---
# <a name="azure-service-fabric-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed wdrożeniem aplikacji w chmurze w środowisku produkcyjnym zapoznaj się z naszą listą najważniejszych i zalecanych najlepszych rozwiązań dotyczących implementowania bezpiecznych klastrów w aplikacji.

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

Dla każdego najlepszego rozwiązania wyjaśniono:

-   Najlepsze rozwiązanie to.
-   Dlaczego należy zaimplementować najlepsze rozwiązanie.
-   Co się stanie, jeśli nie zostanie wdrożone najlepsze rozwiązanie.
-   Jak można dowiedzieć się, jak wdrożyć najlepsze rozwiązanie.

Zalecamy następujące najlepsze rozwiązania w zakresie zabezpieczeń Service Fabric platformy Azure:

-   Do tworzenia bezpiecznych klastrów używaj szablonów Azure Resource Manager i modułu Service Fabric PowerShell.
-   Użyj certyfikatów X. 509.
-   Skonfiguruj zasady zabezpieczeń.
-   Zaimplementuj konfigurację zabezpieczeń Reliable Actors.
-   Skonfiguruj protokół SSL dla Service Fabric platformy Azure.
-   Korzystanie z izolacji sieci i zabezpieczeń za pomocą usługi Azure Service Fabric.
-   Skonfiguruj Azure Key Vault dla zabezpieczeń.
-   Przypisywanie użytkowników do ról.


## <a name="best-practices-for-securing-your-clusters"></a>Najlepsze rozwiązania dotyczące zabezpieczania klastrów

Zawsze używaj bezpiecznego klastra:
-   Implementowanie zabezpieczeń klastra przy użyciu certyfikatów.
-   Zapewnianie dostępu klienta (administratora i tylko do odczytu) za pomocą Azure Active Directory (Azure AD).

Użyj zautomatyzowanych wdrożeń:
-   Za pomocą skryptów można generować, wdrażać i przerzucać wpisy tajne.
-   Przechowuj wpisy tajne w Azure Key Vault i korzystaj z usługi Azure AD, aby uzyskać dostęp do wszystkich innych klientów.
-   Wymagaj uwierzytelniania dla dostępu przez ludzi do wpisów tajnych.

Ponadto należy rozważyć następujące opcje konfiguracji:
-   Tworzenie sieci obwodowych (nazywanych również strefami zdemilitaryzowana, stref DMZ i podsieciami z osłoną) przy użyciu sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) platformy Azure.
-   Dostęp do maszyn wirtualnych klastra lub Zarządzanie klastrem przy użyciu serwerów przechodzenia do Podłączanie pulpitu zdalnego.

Klastry muszą być zabezpieczone, aby uniemożliwić nawiązywanie połączeń nieautoryzowanym użytkownikom, szczególnie w przypadku, gdy klaster działa w środowisku produkcyjnym. Chociaż istnieje możliwość utworzenia niezabezpieczonego klastra, użytkownicy anonimowi mogą łączyć się z klastrem, Jeśli klaster ujawnia punkty końcowe zarządzania do publicznej sieci Internet.

Istnieją trzy [scenariusze](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) implementowania zabezpieczeń klastra przy użyciu różnych technologii:

-   Zabezpieczenia między węzłami: Ten scenariusz zabezpiecza komunikację między maszynami wirtualnymi i komputerami w klastrze. Ta forma zabezpieczeń zapewnia, że tylko komputery autoryzowane do przyłączenia do klastra mogą hostować aplikacje i usługi w klastrze.
W tym scenariuszu klastry działające na platformie Azure lub autonomiczne klastry działające w systemie Windows mogą korzystać z [zabezpieczeń certyfikatów](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) [lub dla](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) maszyn z systemem Windows Server.
-   Zabezpieczenia klient-węzeł: Ten scenariusz zabezpiecza komunikację między klientem Service Fabric i poszczególnymi węzłami w klastrze.
-   Access Control oparte na rolach (RBAC): W tym scenariuszu są używane oddzielne tożsamości (certyfikaty, usługa Azure AD itd.) dla każdej roli klienta i administratora, która uzyskuje dostęp do klastra. Tożsamości ról są określane podczas tworzenia klastra.

>[!NOTE]
>**Zalecenia dotyczące zabezpieczeń klastrów platformy Azure:** Użyj zabezpieczeń usługi Azure AD do uwierzytelniania klientów i certyfikatów na potrzeby zabezpieczeń między węzłami.

Aby skonfigurować autonomiczny klaster systemu Windows, zobacz [Konfigurowanie ustawień dla autonomicznego klastra systemu Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Aby utworzyć bezpieczny klaster, użyj szablonów Azure Resource Manager i modułu Service Fabric PowerShell.
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia bezpiecznego klastra Service Fabric przy użyciu szablonów Azure Resource Manager, zobacz [Tworzenie klastra Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Użyj szablonu Azure Resource Manager:
-   Dostosuj klaster przy użyciu szablonu, aby skonfigurować magazyn zarządzany dla wirtualnych dysków twardych (VHD) maszyn wirtualnych.
-   Wprowadzanie zmian w grupie zasobów przy użyciu szablonu do łatwego zarządzania konfiguracją i inspekcji.

Traktuj konfigurację klastra jako kod:
-   Podczas sprawdzania konfiguracji wdrożenia należy gruntownie.
-   Unikaj używania niejawnych poleceń, aby bezpośrednio modyfikować zasoby.

Wiele aspektów [cyklu życia aplikacji Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) można zautomatyzować. [Moduł Service Fabric PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatyzuje typowe zadania związane z wdrażaniem, uaktualnianiem, usuwaniem i testowaniem aplikacji Service Fabric platformy Azure. Dostępne są również zarządzane interfejsy API i interfejsy API protokołu HTTP dla zarządzania aplikacjami.

## <a name="use-x509-certificates"></a>Korzystanie z certyfikatów X. 509
Zawsze zabezpieczaj klastry za pomocą certyfikatów X. 509 lub zabezpieczeń systemu Windows. Zabezpieczenia są konfigurowane tylko w czasie tworzenia klastra. Nie można włączyć zabezpieczeń po utworzeniu klastra.

Aby określić [certyfikat klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), ustaw wartość właściwości **ClusterCredentialType** na x509. Aby określić certyfikat serwera dla połączeń zewnętrznych, należy ustawić właściwość **ServerCredentialType** na x509.

Ponadto należy przestrzegać następujących praktyk:
-   Utwórz certyfikaty dla klastrów produkcyjnych przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server. Można również uzyskać certyfikaty z zatwierdzonego urzędu certyfikacji.
-   Nie należy używać certyfikatu tymczasowego lub testowego dla klastrów produkcyjnych, jeśli certyfikat został utworzony przy użyciu MakeCert. exe lub podobnego narzędzia.
-   Użyj certyfikatu z podpisem własnym dla klastrów testowych, ale nie dla klastrów produkcyjnych.

Jeśli klaster jest niezabezpieczony, każdy może połączyć się z klastrem anonimowo i wykonać operacje zarządzania. Z tego powodu zawsze zabezpieczaj klastry produkcyjne przy użyciu certyfikatów X. 509 lub zabezpieczeń systemu Windows.

Aby dowiedzieć się więcej o używaniu certyfikatów X. 509, zobacz [Dodawanie lub usuwanie certyfikatów klastra Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Konfiguruj zasady zabezpieczeń
Service Fabric również zabezpiecza zasoby używane przez aplikacje. Zasoby, takie jak pliki, katalogi i certyfikaty, są przechowywane na kontach użytkowników podczas wdrażania aplikacji. Ta funkcja sprawia, że uruchomione aplikacje są bezpieczniejsze od siebie, nawet w udostępnianym środowisku hostowanym.

-   Użyj Active Directory grupy domeny lub użytkownika: Uruchom usługę w ramach poświadczeń dla konta użytkownika lub grupy Active Directory. Upewnij się, że Active Directory lokalnie w domenie, a nie Azure Active Directory. Uzyskaj dostęp do innych zasobów w domenie, którym udzielono uprawnień przy użyciu użytkownika domeny lub grupy. Na przykład zasoby, takie jak udziały plików.

-   Przypisz zasady dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS: Określ właściwość **SecurityAccessPolicy** , aby zastosować zasady **runas** do usługi, gdy manifest usługi deklaruje zasoby punktów końcowych przy użyciu protokołu HTTP. Porty przyłączone do punktów końcowych HTTP są prawidłowo dostępne dla konta użytkownika RunAs, w ramach którego działa usługa. Gdy zasady nie są ustawione, http. sys nie ma dostępu do usługi i można uzyskać błędy z wywołaniami z klienta.

Aby dowiedzieć się, jak używać zasad zabezpieczeń w klastrze Service Fabric, zobacz [Konfigurowanie zasad zabezpieczeń aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Zaimplementuj konfigurację zabezpieczeń Reliable Actors
Service Fabric Reliable Actors jest implementacją wzorca projektowego aktora. Podobnie jak w przypadku każdego wzorca projektowego, decyzja o użyciu określonego wzorca jest oparta na tym, czy problem z oprogramowaniem pasuje do wzorca.

Ogólnie rzecz biorąc, użyj wzorca projektowego aktora, aby ułatwić modelowanie rozwiązań dla następujących problemów z oprogramowaniem lub scenariuszy zabezpieczeń:
-   Przestrzeń problemu obejmuje dużą liczbę (w tysiącach lub więcej) małych, niezależnych i izolowanych jednostek stanu i logiki.
-   Pracujesz z obiektami jednowątkowymi, które nie wymagają znaczącej interakcji ze składnikami zewnętrznymi, w tym stanu zapytań w zestawie aktorów.
-   Wystąpienia aktora nie blokują wywołań z nieprzewidywalnymi opóźnieniami przez wydawanie operacji we/wy.

W Service Fabric, aktory są zaimplementowane w Reliable Actors Framework aplikacji. Ta struktura jest oparta na wzorcu aktora i oparta na [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Każda niezawodna usługa aktora, którą pisze, to podzielona na partycje usługa niezawodna.

Każdy aktor jest definiowany jako wystąpienie typu aktora, tak samo jak obiekt .NET jest wystąpieniem typu .NET. Na przykład **Typ aktora** implementujący funkcje kalkulatora może mieć wiele aktorów tego typu, które są dystrybuowane na różnych węzłach w klastrze. Każdy z rozproszonych aktorów ma unikatową postać identyfikatora aktora.

[Konfiguracje zabezpieczeń replikatora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) służą do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Ta konfiguracja uniemożliwia usługom wzajemne wyświetlanie ruchu związanego z replikacją i zapewnia bezpieczeństwo danych o wysokiej dostępności. Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia zabezpieczenia replikacji.
Konfiguracje replikatora konfiguruje Replikator, który jest odpowiedzialny za zapewnianie niezawodnego stanu dostawcy stanu aktora.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurowanie protokołu SSL dla usługi Azure Service Fabric
Proces uwierzytelniania serwera [uwierzytelnia](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) punkty końcowe zarządzania klastrem do klienta zarządzania. Klient zarządzający rozpoznaje to, że jest on rozmawiany z rzeczywistym klastrem. Ten certyfikat udostępnia również [protokół SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) dla interfejsu API zarządzania https i dla Service Fabric Explorer za pośrednictwem protokołu HTTPS.
Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną przez klaster.

Aby skonfigurować protokół SSL dla aplikacji, należy najpierw uzyskać certyfikat SSL, który został podpisany przez urząd certyfikacji. Urząd certyfikacji jest zaufaną osobą trzecią, która wystawia certyfikaty na potrzeby zabezpieczeń SSL. Jeśli nie masz jeszcze certyfikatu SSL, musisz uzyskać go od firmy, która sprzedaje certyfikaty SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL na platformie Azure:
-   Certyfikat musi zawierać klucz prywatny.

-   Należy utworzyć certyfikat do wymiany kluczy i można go wyeksportować do pliku wymiany informacji osobistych (pfx).

-   Nazwa podmiotu certyfikatu musi być zgodna z nazwą domeny używaną w celu uzyskania dostępu do usługi w chmurze.

    - Uzyskaj niestandardową nazwę domeny, która ma być używana do uzyskiwania dostępu do usługi w chmurze.
    - Zażądaj certyfikatu od urzędu certyfikacji z nazwą podmiotu zgodną z niestandardową nazwą domeny usługi. Na przykład jeśli niestandardowa nazwa domeny to __contoso__ **. com**, certyfikat z urzędu certyfikacji powinien mieć nazwę podmiotu **. contoso.com** lub __www__ **. contoso.com**.

    >[!NOTE]
    >Nie można uzyskać certyfikatu SSL z urzędu certyfikacji dla domeny __cloudapp__ **.NET** .

-   Certyfikat musi mieć co najmniej 2 048-bitowe szyfrowanie.

Protokół HTTP jest niezabezpieczony i podlega atakom podsłuchu. Dane przesyłane za pośrednictwem protokołu HTTP są wysyłane jako zwykły tekst z przeglądarki sieci Web do serwera sieci Web lub między innymi punktami końcowymi. Osoby atakujące mogą przechwycić i wyświetlić dane poufne, które są wysyłane za pośrednictwem protokołu HTTP, takie jak szczegóły karty kredytowej i logowania do konta. Gdy dane są wysyłane lub ogłaszane za pośrednictwem sieci za pośrednictwem protokołu HTTPS, protokół SSL gwarantuje, że poufne informacje są szyfrowane i zabezpieczone przed przechwyceniem.

Aby dowiedzieć się więcej o korzystaniu z certyfikatów SSL, zobacz [Konfigurowanie protokołu SSL dla aplikacji platformy Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Korzystanie z izolacji sieci i zabezpieczeń za pomocą usługi Azure Service Fabric
Skonfiguruj NodeType bezpieczny klaster przy użyciu [szablonu Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) jako przykładu. Kontrolowanie ruchu przychodzącego i wychodzącego w sieci przy użyciu szablonów i sieciowych grup zabezpieczeń.

Szablon zawiera sieciowej grupy zabezpieczeń dla każdego z zestawów skalowania maszyn wirtualnych i służy do sterowania ruchem do i z zestawu. Reguły są domyślnie skonfigurowane tak, aby zezwalały na cały ruch potrzebny dla usług systemowych i portów aplikacji określonych w szablonie. Przejrzyj te reguły i wprowadź wszelkie zmiany, aby dostosować je do Twoich potrzeb, w tym dodawanie nowych reguł dla aplikacji.

Aby uzyskać więcej informacji, zobacz [typowe scenariusze sieci dotyczące platformy Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurowanie Azure Key Vault na potrzeby zabezpieczeń
Service Fabric używa certyfikatów do zapewnienia uwierzytelniania i szyfrowania na potrzeby zabezpieczania klastra i jego aplikacji.

Service Fabric używa certyfikatów X. 509 w celu zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji. Azure Key Vault służy do [zarządzania certyfikatami](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) dla klastrów Service Fabric na platformie Azure. Dostawca zasobów platformy Azure, który tworzy klastry pobiera certyfikaty z magazynu kluczy. Następnie dostawca instaluje certyfikaty na maszynach wirtualnych, gdy klaster zostanie wdrożony na platformie Azure.

Istnieje relacja między [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), klastrem Service Fabric i dostawcą zasobów, który korzysta z certyfikatów. Po utworzeniu klastra informacje o relacji certyfikatu są przechowywane w magazynie kluczy.

Istnieją dwa podstawowe kroki konfigurowania magazynu kluczy:
1. Utwórz grupę zasobów dla magazynu kluczy.

    Zalecamy umieszczenie magazynu kluczy w jego własnej grupie zasobów. Ta akcja pomaga zapobiec utracie kluczy i wpisów tajnych w przypadku usunięcia innych grup zasobów, takich jak magazyn, obliczenia lub grupa zawierająca klaster. Grupa zasobów zawierająca Magazyn kluczy musi znajdować się w tym samym regionie co klaster, który go używa.

2. Utwórz magazyn kluczy w nowej grupie zasobów.

    Magazyn kluczy musi być włączony do wdrożenia. Dostawca zasobów obliczeniowych może następnie pobrać certyfikaty z magazynu i zainstalować je na wystąpieniach maszyn wirtualnych.

Aby dowiedzieć się więcej na temat konfigurowania magazynu kluczy, zobacz [co to jest Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji do reprezentowania klastra Przypisz użytkowników do ról, które są obsługiwane przez Service Fabric: tylko do odczytu i administrator. Można przypisać te role przy użyciu Azure Portal.

>[!NOTE]
> Aby uzyskać więcej informacji o korzystaniu z ról w Service Fabric, zobacz [Access Control oparte na rolach dla klientów Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Usługa Azure Service Fabric obsługuje dwa typy kontroli dostępu dla klientów, którzy są połączeni z [klastrem Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administrator i użytkownik. Administrator klastra może użyć kontroli dostępu, aby ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Kontrola dostępu sprawia, że klaster jest bezpieczniejszy.

## <a name="next-steps"></a>Kolejne kroki

- [Lista kontrolna zabezpieczeń Service Fabric](service-fabric-checklist.md)
- Skonfiguruj [środowisko deweloperskie](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)Service Fabric.
- Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
