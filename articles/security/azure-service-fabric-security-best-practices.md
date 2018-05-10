---
title: Azure Service Fabric najlepszych rozwiązań dotyczących zabezpieczeń | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące zabezpieczeń sieć szkieletowa usług Azure.
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
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: b908589903d243b2d284e2a23b6111785229c16f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric najlepszych rozwiązań dotyczących zabezpieczeń
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed przystąpieniem do wdrażania aplikacji w środowisku produkcyjnym chmury, przejrzyj naszej listy ważne i zalecane najlepsze rozwiązania dotyczące wdrażania bezpiecznych klastrów w aplikacji.

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu. 

Dla każdego najlepszym rozwiązaniem prezentujemy zasady:

-   Co to jest najlepszym rozwiązaniem.
-   Dlaczego należy zaimplementować najlepszym rozwiązaniem.
-   Co może nastąpić, jeśli nie implementują najlepszym rozwiązaniem.
-   Jak można uzyskać do zaimplementowania najlepszym rozwiązaniem.

Zaleca się następujące zabezpieczeń usługi Azure Service Fabric najlepsze rozwiązania:

-   Do tworzenia bezpiecznych klastrów, należy użyć modułu programu PowerShell usługi Service Fabric i szablony usługi Azure Resource Manager.
-   Za pomocą certyfikatów X.509.
-   Konfigurowanie zasad zabezpieczeń.
-   Implementuje Reliable Actors konfiguracji zabezpieczeń.
-   Konfigurowanie protokołu SSL dla sieci szkieletowej usług Azure.
-   Korzystania z usługi Azure Service Fabric izolacji sieci i zabezpieczeń.
-   Konfigurowanie usługi Azure Key Vault dla zabezpieczeń.
-   Przypisywanie użytkowników do ról.


## <a name="best-practices-for-securing-your-clusters"></a>Najlepsze rozwiązania dotyczące zabezpieczania klastrów

Zawsze używaj bezpiecznej klastra:
-   Implementowanie zabezpieczeń klastra za pomocą certyfikatów.
-   Dostęp klienta (admin i tylko do odczytu) przy użyciu usługi Azure Active Directory (Azure AD).

Użyj zautomatyzowanych wdrożeń:
-   Aby wygenerować, wdrażanie i kluczy tajnych są przerzucane za pomocą skryptów.
-   Przechowywanie kluczy tajnych w magazynie kluczy Azure i przy użyciu usługi Azure AD dla wszystkich innych dostępu klientów.
-   Wymagaj uwierzytelniania dla człowieka dostęp do kluczy tajnych.

Ponadto należy wziąć pod uwagę następujące opcje konfiguracji:
-   Tworzenie sieci obwodowej (znanej także jako zdemilitaryzowaną stref, sieci DMZ i podsieci ekranowanej) przy użyciu grup zabezpieczeń sieci Azure (NSG).
-   Dostęp do klastra maszynach wirtualnych (VM) lub zarządzać klastrem przy użyciu serwery przeskoku Podłączanie pulpitu zdalnego.

Musi być zabezpieczona klastrów, aby zapobiec nieautoryzowanemu połączenie, szczególnie, gdy klaster jest uruchomiony w środowisku produkcyjnym. Chociaż można utworzyć klaster niezabezpieczoną, anonimowe użytkownicy mogą łączyć się klastra, jeśli klaster przedstawia punkty końcowe zarządzania do publicznej sieci internet.

Istnieją trzy [scenariusze](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) stosowania zabezpieczeń klastra przy użyciu różnych technologii:

-   Zabezpieczenia węzła do węzła: w tym scenariuszu zabezpiecza komunikację między maszynami wirtualnymi i komputerów w klastrze. Ten formularz zabezpieczeń zapewnia, że tylko te komputery, które są autoryzowani do przyłączenia klaster może obsługiwać aplikacje i usługi w klastrze.
W tym scenariuszu, klastrów działających na platformie Azure lub klastry autonomicznego, uruchomionych w systemie Windows, można użyć [certyfikatu zabezpieczeń](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) lub [zabezpieczenia systemu Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) dla komputerów z systemem Windows Server.
-   Węzeł Klient zabezpieczeń: w tym scenariuszu zabezpieczania komunikacji między klientem sieci szkieletowej usług pojedynczych węzłów w klastrze.
-   Oparta na rolach kontroli dostępu (RBAC): w tym scenariuszu oddzielne tożsamości (certyfikaty, usługi Azure AD i tak dalej) dla każdego administratora i klienta roli użytkownika uzyskującego dostęp do klastra. Po utworzeniu klastra można określić tożsamości roli.

>[!NOTE]
>**Zalecana ze względów bezpieczeństwa w przypadku klastrów platformy Azure:** zabezpieczeń użycia usługi Azure AD na potrzeby uwierzytelniania klientów i certyfikatów dla zabezpieczeń węzła do węzła.

Aby skonfigurować autonomiczny klastra systemu Windows, zobacz [konfigurowania ustawień dla klastra systemu Windows autonomiczny](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Użyj modułu programu PowerShell usługi Service Fabric i szablony usługi Azure Resource Manager, aby utworzyć bezpieczne klastra.
Aby uzyskać instrukcje utworzyć bezpieczne klastra sieci szkieletowej usług przy użyciu szablonów usługi Azure Resource Manager, zobacz [tworzenia klastra usługi sieć szkieletowa](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Szablon usługi Azure Resource Manager:
-   Dostosowywanie klastra przy użyciu szablonu do konfigurowania zarządzanych magazynu dla wirtualnych dysków twardych (VHD) maszyny Wirtualnej.
-   Stacja zmian w danej grupie zasobów przy użyciu szablonu dla konfiguracji łatwe zarządzanie i inspekcji.

Traktuj konfiguracji klastra jako kodu:
-   Być dokładnym podczas sprawdzania konfiguracji wdrożenia.
-   Unikaj używania polecenia niejawne bezpośrednio zmodyfikować zasobów.

Wiele aspektów [cyklem życia aplikacji usługi sieć szkieletowa](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) można zautomatyzować. [Modułu programu PowerShell dla usługi sieci szkieletowej](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) zautomatyzowanie typowych zadań związanych z wdrażanie, uaktualnianie, usuwanie i testowanie aplikacji sieci szkieletowej usług Azure. Zarządzane interfejsy API i interfejsów API HTTP do zarządzania aplikacjami są również dostępne.

## <a name="use-x509-certificates"></a>Użyj certyfikatów X.509
Zawsze Zabezpieczanie klastrów za pomocą certyfikatów X.509 lub zabezpieczenia systemu Windows. Tylko skonfigurować zabezpieczenia, w czasie tworzenia klastra. Nie jest możliwe włączyć zabezpieczeń po utworzeniu klastra.

Aby określić [certyfikatu klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), ustaw wartość **ClusterCredentialType** X509 dla właściwości. Aby określić certyfikat serwera dla połączeń zewnętrznych, ustaw **ServerCredentialType** X509 dla właściwości.

Ponadto należy wykonać następujące rozwiązania:
-   Utwórz certyfikaty dla klastrów produkcyjnych, używając poprawnie skonfigurowanych usług certyfikatów systemu Windows Server. Możesz również uzyskać certyfikatów od urzędu certyfikacji zatwierdzonych (CA).
-   Nigdy nie używaj tymczasowej lub badania certyfikatu dla klastrów produkcyjnych, jeśli certyfikat został utworzony przy użyciu MakeCert.exe lub podobnego narzędzia.
-   Użyj certyfikatu z podpisem własnym dla klastrów testowych, ale nie dla klastrów produkcyjnych.

Jeśli klaster jest niebezpieczne, każda osoba, która anonimowe połączenia z klastrem i wykonywać operacje zarządzania. Z tego powodu należy zawsze zabezpieczać klastrów produkcyjnych, za pomocą certyfikatów X.509 lub zabezpieczenia systemu Windows.

Aby dowiedzieć się więcej o korzystaniu z certyfikatów X.509, zobacz [Dodaj lub Usuń certyfikaty dla klastra usługi sieć szkieletowa](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Konfigurowania zasad zabezpieczeń
Sieć szkieletowa usług zabezpiecza również zasoby, które są używane przez aplikacje. Pliki i katalogi, takich jak zasoby oraz certyfikaty są przechowywane na kontach użytkowników, gdy aplikacja jest wdrażana. Ta funkcja umożliwia uruchamianie aplikacji w bardziej bezpieczne od siebie, nawet w środowisku hostowanej udostępnionego.

-   Użyj grupy domeny usługi Active Directory lub użytkownika: uruchamianie usługi przy użyciu poświadczeń dla konta użytkownika lub grupy usługi Active Directory. Należy użyć usługi Active Directory lokalnie w domenie i nie usługi Azure Active Directory. Dostęp do innych zasobów w domenie, które ma odpowiednie uprawnienia za pomocą użytkownika domeny lub grupy. Na przykład zasobów, takich jak udziały plików.

-   Przypisać zasady zabezpieczeń dostępu dla punktów końcowych HTTP i HTTPS: Określ **SecurityAccessPolicy** właściwości, aby zastosować **RunAs** zasad z usługą podczas manifestu usługi deklaruje zasobów punktu końcowego protokołu HTTP. Porty przydzielone do punktów końcowych HTTP są listami poprawnie kontroli dostępu konto użytkownika Uruchom jako, które uruchamiana usługa. Gdy zasada nie jest ustawiona, sterownik http.sys nie ma dostępu do usługi i błędów z wywołań można uzyskać od klienta.

Aby dowiedzieć się, jak używać zasad zabezpieczeń w klastrze usługi sieć szkieletowa usług, zobacz [konfigurowania zasad zabezpieczeń dla aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Wdrożenie konfiguracji zabezpieczeń Reliable Actors
Usługa sieci szkieletowej Reliable Actors jest implementacją wzorca projektowego aktora. Podobnie jak w przypadku dowolnego wzorcu projektowym oprogramowania decyzji o użyciu z określonym wzorcem opiera się na Określa, czy problem z oprogramowaniem pasuje do wzorca.

Ogólnie rzecz biorąc skorzystaj wzorca projektowego aktora modelu rozwiązania następujących problemów oprogramowania lub scenariusze zabezpieczeń:
-   Obszar problem obejmuje dużą liczbą (tysięcy lub więcej) jednostek mały, niezależne i izolowane stanu i logiki.
-   Pracujesz jednowątkowe obiekty niewymagających znaczących interakcji z zewnętrznego składników, w tym zapytań stanu w zestawie złośliwych użytkowników.
-   Swoich wystąpień aktora nie blokuj obiektów wywołujących nieprzewidywalne opóźnień wysyłając operacji We/Wy.

W sieci szkieletowej usług podmioty są realizowane w ramach aplikacji Reliable Actors. Ta struktura jest oparte na wzorcu aktora i oparty na [niezawodne usługi sieć szkieletowa usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Każdej usługi niezawodnego aktora, który można zapisać jest partycjonowany usługi stanowej niezawodne.

Każdy aktora jest zdefiniowany jako wystąpienie typu aktora identyczny sposób obiektu .NET. wystąpienia typu .NET. Na przykład **typ aktora** czy implementuje funkcje Kalkulator może mieć wiele podmiotów tego typu rozproszonych w różnych węzłach w klastrze. Każdy rozproszonej podmiotów jednoznacznie charakteryzuje się identyfikator aktora.

[Konfiguracje zabezpieczeń replikatora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) są używane do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Ta konfiguracja uniemożliwia wyświetlanie siebie nawzajem ruch związany z replikacją usług i zapewnia bezpieczny wysokiej dostępności danych. Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.
Konfiguracje replikatora skonfiguruj replikatora odpowiedzialną za tworzenie wysoce niezawodne stanu dostawcy stanu aktora.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurowanie protokołu SSL dla usługi Azure Service Fabric
Proces uwierzytelniania serwera [uwierzytelnia](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) punktów końcowych klastra zarządzania do klienta zarządzania. Klient zarządzania rozpoznaje, że rozmawia do rzeczywistego klastra. Ten certyfikat zawiera również [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) dla interfejsu API zarządzania HTTPS i Service Fabric Explorer za pośrednictwem protokołu HTTPS.
Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji, nazwa podmiotu certyfikatu musi odpowiadać nazwa domeny niestandardowej, której użyjesz dla klastra.

Aby skonfigurować protokół SSL dla aplikacji, należy najpierw uzyskać certyfikat SSL, który został podpisany przez urząd certyfikacji. Urząd certyfikacji jest zaufany innej firmy, który wystawia certyfikaty dla protokołu SSL ze względów bezpieczeństwa. Jeśli nie masz już certyfikat SSL, należy uzyskać od firmy, która sprzedaje certyfikatów SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL na platformie Azure:
-   Certyfikat musi zawierać klucz prywatny.

-   Certyfikat musi zostać utworzony na potrzeby wymiany kluczy i być możliwy do eksportu do pliku informacji osobistych (pfx) programu exchange.

-   Nazwa podmiotu certyfikatu musi odpowiadać nazwie domeny, który umożliwia dostęp do usługi w chmurze.

    - Możliwość nabycia niestandardowej nazwy domeny do użycia na potrzeby uzyskiwania dostępu do usługi w chmurze.
    - Żądanie certyfikatu od urzędu certyfikacji z nazwą podmiotu, który odpowiada nazwie domeny niestandardowej z usługą. Na przykład jeśli nazwą domeny niestandardowej jest __contoso__**.com**, certyfikat z urzędu certyfikacji powinien mieć nazwę podmiotu **. contoso.com** lub __www__ **. contoso.com**.

    >[!NOTE]
    >Nie można uzyskać certyfikatu SSL z urzędu certyfikacji dla __cloudapp__**.net** domeny.
    
-   Certyfikat należy użyć co najmniej 2048 bitowego szyfrowania.

Protokół HTTP jest niebezpieczne i narażone na ataki polegające. Dane są przesyłane za pośrednictwem protokołu HTTP są wysyłane jako zwykły tekst z przeglądarki sieci web na serwerze sieci web lub między innymi punktami końcowymi. Osoby atakujące mogą przechwytywać i wyświetlić dane poufne, które są wysyłane za pośrednictwem protokołu HTTP, takie jak karty kredytowej i konta logowania. Gdy dane są wysyłane lub opublikowane za pośrednictwem przeglądarki, za pośrednictwem protokołu HTTPS, SSL zapewnia, że informacje poufne jest szyfrowane i chronione przed przechwyceniem.

Aby dowiedzieć się więcej na temat korzystania z certyfikatów SSL, zobacz [Konfigurowanie protokołu SSL dla aplikacji Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Izolacja sieci i zabezpieczeń za pomocą usługi Azure Service Fabric
Konfigurowanie klastra bezpiecznego 3 nodetype za pomocą [szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) jako próbka. Kontroli ruchu sieciowego przychodzącego i wychodzącego za pomocą szablonu i grup zabezpieczeń sieci.

Szablon ma grupy NSG dla każdej zestawy skalowania maszyny wirtualnej i jest używana do sterowania ruch do i z zestawu. Reguły są domyślnie skonfigurowane, aby zezwolić na cały ruch niezbędne do systemu usług i portów aplikacji określonych w szablonie. Przejrzyj te reguły i wprowadź wymagane zmiany do własnych potrzeb, dodając nowe reguły dla aplikacji.

Aby uzyskać więcej informacji, zobacz [typowych scenariuszy sieciowych dla sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurowanie usługi Azure Key Vault dla zabezpieczeń
Aby zapewnić uwierzytelnianie i szyfrowanie Zabezpieczanie klastra i jego aplikacji sieci szkieletowej usług korzysta z certyfikatów.

Sieć szkieletowa usług korzysta z certyfikatów X.509 Zabezpieczanie klastra i udostępnia funkcje zabezpieczeń aplikacji. Użyj usługi Azure Key Vault w celu [zarządzanie certyfikatami](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) dla klastrów sieci szkieletowej usług platformy Azure. Dostawcy zasobów platformy Azure, która tworzy klastry pobiera certyfikaty z magazynu kluczy. Dostawca instaluje certyfikaty na maszynach wirtualnych podczas wdrażania klastra na platformie Azure.

Certyfikat relację między [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), klaster sieci szkieletowej usług i dostawcy zasobów, która używa certyfikatów. Podczas tworzenia klastra informacje o relacji certyfikatów są przechowywane w magazynie kluczy.

Istnieją dwa podstawowe kroki, aby skonfigurować magazyn kluczy:
1. Utwórz grupę zasobów, w szczególności dla magazynu kluczy.

    Zaleca się umieszczenie magazynu kluczy w jego własnej grupy zasobów. Dzięki temu można zapobiec utracie kluczy i kluczy tajnych w przypadku innych grup zasobów zostaną usunięte, takiego jak magazyn, obliczeń lub grupy zawierającej klaster. Grupy zasobów, która zawiera magazynu kluczy musi być w tym samym regionie co klaster, który go używa.

2. Tworzenie magazynu kluczy w nowej grupy zasobów.

    Magazyn kluczy musi być włączona dla wdrożenia. Dostawcy zasobów obliczeniowych można uzyskać certyfikaty z magazynu i zainstalowania go na wystąpień maszyn wirtualnych.

Aby dowiedzieć się więcej na temat sposobu konfigurowania magazynu kluczy, zobacz [wprowadzenie do usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji do reprezentowania klastra przypisać użytkowników do ról, które są obsługiwane przez usługi Service Fabric: tylko do odczytu i administratora. Te role można przypisać za pomocą portalu Azure.

>[!NOTE]
> Aby uzyskać więcej informacji o korzystaniu z ról w sieci szkieletowej usług, zobacz [opartej na rolach kontroli dostępu dla klientów usługi sieć szkieletowa](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Sieć szkieletowa usług Azure obsługuje dwa typy kontroli dostępu dla klientów, które są podłączone do [klastra sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administratora i użytkownika. Administrator klastra można użyć kontroli dostępu, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników. Kontrola dostępu sprawia, że klaster jest bardziej bezpieczne.

## <a name="next-steps"></a>Kolejne kroki
- Konfigurowanie sieci szkieletowej usług [środowisko projektowe](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Dowiedz się więcej o [opcje pomocy technicznej usługi sieć szkieletowa](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
