---
title: Usługa Azure Service Fabric najlepszych rozwiązań dotyczących zabezpieczeń | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 8bafc4a95ca9af4567ed70c190a72f3b351da47c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611520"
---
# <a name="azure-service-fabric-security-best-practices"></a>Usługa Azure Service Fabric najlepsze rozwiązania dotyczące zabezpieczeń
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed przystąpieniem do wdrażania aplikacji w chmurze w środowisku produkcyjnym, Przejrzyj naszą listę podstawowych i zalecane najlepsze rozwiązania dotyczące wdrażania zabezpieczonych klastrów w aplikacji.

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

Dla każdego najlepszym rozwiązaniem jest Wyjaśnijmy:

-   Co to jest najlepszym rozwiązaniem.
-   Dlaczego należy zaimplementować najlepszym rozwiązaniem.
-   Co może nastąpić, jeśli nie implementują najlepsze rozwiązanie.
-   Jak można znaleźć implementacji najlepszym rozwiązaniem.

Następujące zabezpieczeń usługi Azure Service Fabric firma Microsoft zaleca najlepsze rozwiązania:

-   Użyj szablonów usługi Azure Resource Manager i moduł Service Fabric programu PowerShell do tworzenia zabezpieczonych klastrów.
-   Za pomocą certyfikatów X.509.
-   Podczas konfigurowania zasad zabezpieczeń.
-   Implementowanie interfejsu Reliable Actors w konfiguracji zabezpieczeń.
-   Konfigurowanie certyfikatu SSL dla usługi Azure Service Fabric.
-   Za pomocą izolacji sieci i zabezpieczeń usługi Azure Service Fabric.
-   Konfigurowanie usługi Azure Key Vault dla zabezpieczeń.
-   Przypisywanie użytkowników do ról.


## <a name="best-practices-for-securing-your-clusters"></a>Najlepsze rozwiązania dotyczące zabezpieczania klastrów

Zawsze używaj zabezpieczonego klastra:
-   Zaimplementować zabezpieczenia klastra przy użyciu certyfikatów.
-   Zapewniania dostępu klienta (administratora i tylko do odczytu) przy użyciu usługi Azure Active Directory (Azure AD).

Użyj automatycznego wdrożenia:
-   Generowanie, wdrażanie i przerzucić klucze tajne za pomocą skryptów.
-   Store wpisy tajne w usłudze Azure Key Vault i używaj usługi Azure AD dla wszystkich innych dostępu klientów.
-   Wymagaj uwierzytelniania dla człowieka dostępu do kluczy tajnych.

Ponadto należy wziąć pod uwagę następujące opcje konfiguracji:
-   Tworzenie sieci obwodowej (znanej także jako zdemilitaryzowaną stref, stref DMZ i podsieci ekranowanej) przy użyciu sieciowych grup zabezpieczeń (NSG) platformy Azure.
-   Dostęp do klastra maszyny wirtualne (VM) lub zarządzania klastrem przy użyciu serwery skoku Podłączanie pulpitu zdalnego.

Klastry usługi musi być zabezpieczona uniemożliwiające nieupoważnionym użytkownikom pozwalać na połączenie, szczególnie, gdy klaster jest uruchomiona w środowisku produkcyjnym. Chociaż istnieje możliwość utworzenia z niezabezpieczonym klastrem, anonimowe użytkownicy mogli nawiązywać połączenie klastra, jeśli klaster udostępnia punktów końcowych zarządzania do publicznej sieci internet.

Dostępne są trzy [scenariuszy](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) implementowania zabezpieczenia klastra przy użyciu różnych technologii:

-   Zabezpieczenia węzła do węzła: W tym scenariuszu zabezpiecza komunikację między maszynami wirtualnymi i komputerów w klastrze. Ta forma zabezpieczeń gwarantuje, że tylko te komputery, które są autoryzowane do przyłączenia się do klastra można hostować aplikacje i usługi w klastrze.
W tym scenariuszu, klastrów działających na platformie Azure lub autonomicznych klastrów, w systemie Windows, można użyć albo [certyfikatów zabezpieczeń](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) lub [zabezpieczeń Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) dla maszyn z systemem Windows Server.
-   Węzeł klienta zabezpieczeń: W tym scenariuszu zabezpiecza komunikację między klientem usługi Service Fabric i poszczególnych węzłów w klastrze.
-   Kontrola dostępu oparta na rolach (RBAC): W tym scenariuszu osobne tożsamości (certyfikaty usługi Azure AD, i tak dalej) dla każdego administratora i użytkownika roli klienta, który uzyskuje dostęp do klastra. Tożsamości roli należy określić podczas tworzenia klastra.

>[!NOTE]
>**Zalecenie dotyczące zabezpieczeń dla klastrów platformy Azure:** Użyj zabezpieczeń usługi Azure AD do uwierzytelniania klientów i certyfikaty dla zabezpieczeń między węzłami.

Aby skonfigurować autonomicznego klastra Windows, zobacz [konfigurowania ustawień dla autonomicznego klastra Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Użyj modułu Service Fabric programu PowerShell i szablonów usługi Azure Resource Manager, aby utworzyć z bezpiecznym klastrem.
Aby uzyskać instrukcje krok po kroku tworzenie bezpiecznego klastra usługi Service Fabric przy użyciu szablonów usługi Azure Resource Manager, zobacz [tworzenia klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Wybierz szablon usługi Azure Resource Manager:
-   Dostosowywanie klastra za pomocą szablonu do konfigurowania magazynu zarządzanego dla maszyny Wirtualnej wirtualnych dysków twardych (VHD).
-   Podejmuj zmiany do grupy zasobów na podstawie przy użyciu szablonu do zarządzania konfiguracją łatwy i inspekcji.

Traktuj konfigurację klastra, jak kod:
-   Sprawdzanie konfiguracji wdrażania, być dokładne.
-   Należy unikać używania poleceń niejawne bezpośrednio modyfikować swoje zasoby.

Wiele aspektów [cyklem życia aplikacji usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) można zautomatyzować. [Modułu Service Fabric programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatyzuje typowe zadania dotyczące wdrażanie, uaktualnianie, usuwanie i testowanie aplikacji w usłudze Azure Service Fabric. Zarządzane interfejsy API i interfejsów API protokołu HTTP do zarządzania aplikacjami są także dostępne.

## <a name="use-x509-certificates"></a>Używanie certyfikatów X.509
Klastry usługi należy zawsze zabezpieczać przy użyciu certyfikatów X.509 lub zabezpieczeń Windows. Zabezpieczenia są konfigurowane tylko w momencie tworzenia klastra. Nie jest możliwe włączyć zabezpieczeń po utworzeniu klastra.

Aby określić [certyfikatu klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), ustaw wartość **ClusterCredentialType** właściwość X509. Aby określić certyfikat serwera dla połączeń zewnętrznych, ustaw **ServerCredentialType** właściwość X509.

Ponadto należy wykonać następujące rozwiązania:
-   Używając poprawnie skonfigurowanych usług certyfikatów systemu Windows Server, należy utworzyć certyfikatów w przypadku klastrów produkcyjnych. Możesz również uzyskać certyfikatów od urzędu certyfikacji zatwierdzonych (CA).
-   Nigdy nie używaj tymczasowy lub sprawdzić certyfikatu w przypadku klastrów produkcyjnych, czy certyfikat został utworzony przy użyciu MakeCert.exe lub podobnego narzędzia.
-   Użyj certyfikatu z podpisem własnym dla klastrów testowych, ale nie w przypadku klastrów produkcyjnych.

Jeśli klaster jest niezabezpieczony, każdy anonimowo połączyć się z klastrem i wykonywać operacje zarządzania. Z tego powodu należy zawsze zabezpieczyć klastrów produkcyjnych przy użyciu certyfikatów X.509 lub zabezpieczeń Windows.

Aby dowiedzieć się więcej o korzystaniu z certyfikatów X.509, zobacz [apletu Dodaj lub Usuń certyfikaty dla klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Podczas konfigurowania zasad zabezpieczeń
Usługa Service Fabric zabezpiecza również zasoby, które są używane przez aplikacje. Zasoby, takie jak pliki i katalogi, i certyfikaty są przechowywane na kontach użytkowników, gdy aplikacja jest wdrażana. Ta funkcja umożliwia uruchamianie aplikacji w bardziej bezpieczne od siebie nawzajem, nawet w środowisku współdzielonym hostowanej.

-   Użyj grupy domeny usługi Active Directory lub użytkownika: Uruchom usługę przy użyciu poświadczeń użytkownika usługi Active Directory lub konta grupy. Pamiętaj użyć usługi Active Directory w środowisku lokalnym w ramach domeny i nie usługi Azure Active Directory. Dostęp do innych zasobów w domenie, które ma odpowiednie uprawnienia za pomocą użytkownika domeny lub grupy. Na przykład zasoby, takie jak udziały plików.

-   Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS: Określ **SecurityAccessPolicy** właściwości, aby zastosować **RunAs** zasad z usługą podczas manifestu usługi deklaruje zasobów punktu końcowego za pośrednictwem protokołu HTTP. Porty przydzielone do punktów końcowych HTTP są poprawnie kontrolowany dostęp do listy dla konta użytkownika Uruchom jako, które usługa będzie uruchamiana. Jeśli zasada nie jest ustawiona, sterownik http.sys nie ma dostępu do usługi i niepowodzeń wywołań można uzyskać od klienta.

Aby dowiedzieć się, jak używać zasad zabezpieczeń w klastrze usługi Service Fabric, zobacz [podczas konfigurowania zasad zabezpieczeń dla aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementowanie konfiguracji zabezpieczeń elementów Reliable Actors
Elementy Reliable Actors usługi Service Fabric to implementacja wzorca projektowego aktora. Podobnie jak w przypadku wszelkie wzorzec projektowania oprogramowania zdecydować, czy użyć określonego wzorca zależy od tego, czy problem z oprogramowaniem pasuje do wzorca.

Ogólnie rzecz biorąc Użyj wzorca projektowego aktora w celu łatwiejszego rozwiązania z modelami dla następujących problemów oprogramowania lub scenariusze zabezpieczeń:
-   Obszar problemu obejmuje dużą liczbą (tysięcy lub więcej) małe, niezależne i izolowanych jednostek, stanu i logiki.
-   Pracujesz z obiektami jednowątkowe, które nie wymagają znaczących interakcji ze składników zewnętrznych, w tym, badanie stanu w zestawie aktorów.
-   Wystąpienia aktora nie blokuj obiektów wywołujących nieprzewidywalne opóźnień, wydając operacji We/Wy.

W usłudze Service Fabric aktorów są implementowane w ramach aplikacji Reliable Actors. Ta struktura jest oparta na wzorcu aktora i wbudowane w górnej części [usług Reliable Services usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Każdej usługi reliable actors, które piszesz jest podzielone na partycje niezawodnej stanowej usługi.

Każdego aktora jest zdefiniowany jako wystąpienie typu aktora taka sama jak sposób, w jaki obiekt .NET jest wystąpieniem typu .NET. Na przykład **typ aktora** że implementuje funkcje Kalkulator może mieć wielu aktorów tego typu, rozproszonych w różnych węzłach w klastrze. Każdy z uczestnikami rozproszonej jednoznacznie charakteryzuje się identyfikator aktora.

[Konfiguracje zabezpieczeń replikatora](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) służą do bezpiecznego kanału komunikacji, który jest używany podczas replikacji. Ta konfiguracja uniemożliwia wyświetlanie siebie nawzajem ruch związany z replikacją usług i zapewnia bezpieczne danych o wysokiej dostępności. Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.
Konfiguracje replikatora skonfiguruj replikatora, który jest odpowiedzialny za wprowadzanie stan dostawcy stanu aktora o wysokiej niezawodności.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurowanie certyfikatu SSL dla usługi Azure Service Fabric
Proces uwierzytelniania serwera [uwierzytelnia](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) punktów końcowych zarządzania klastrem na kliencie zarządzania. Klient zarządzania rozpoznaje, jest najbardziej rozmownych na rzeczywistym klastrem. Ten certyfikat zapewnia również [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) dla interfejsu API zarządzania protokołu HTTPS i narzędzia Service Fabric Explorer za pośrednictwem protokołu HTTPS.
Musisz uzyskać niestandardową nazwę domeny dla klastra. Gdy w przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi odpowiadać nazwie domeny niestandardowej, używanego dla klastra.

Aby skonfigurować protokół SSL dla aplikacji, należy najpierw uzyskać certyfikat SSL, który został podpisany przez urząd certyfikacji. Urząd certyfikacji jest zaufany innych firm, które wystawiają certyfikaty ze względów bezpieczeństwa protokołu SSL. Jeśli nie masz jeszcze certyfikatu SSL, musisz go uzyskać od firmy, która sprzedaje certyfikaty SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL na platformie Azure:
-   Certyfikat musi zawierać klucz prywatny.

-   Certyfikat musi zostać utworzona na potrzeby wymiany kluczy i być możliwy do eksportu do pliku wymiany (pfx) dane osobowe.

-   Nazwa podmiotu certyfikatu musi odpowiadać nazwie domeny, która umożliwia dostęp do usługi w chmurze.

    - Uzyskać niestandardową nazwę domeny na potrzeby uzyskiwania dostępu do usługi w chmurze.
    - Żądanie certyfikatu od urzędu certyfikacji z nazwą podmiotu zgodną z usługą niestandardowej nazwy domeny. Na przykład, jeśli nazwa domeny niestandardowej jest __contoso__ **.com**, certyfikat z urzędu certyfikacji powinien mieć nazwę podmiotu **. contoso.com** lub __www__ **. contoso.com**.

    >[!NOTE]
    >Nie można uzyskać certyfikatu SSL z urzędu certyfikacji dla __cloudapp__ **.net** domeny.

-   Certyfikat należy użyć co najmniej szyfrowanie 2048 bitowych.

Protokół HTTP jest niebezpieczne i ataki polegające na przechwytywaniu komunikacji. Dane są przesyłane za pośrednictwem protokołu HTTP są wysyłane jako zwykły tekst w przeglądarce sieci web na serwerze sieci web lub od innych punktów końcowych. Osoby atakujące mogą przechwytywać i wyświetlić dane poufne, które są wysyłane za pośrednictwem protokołu HTTP, takich jak karty kredytowej i logowania do konta. Gdy dane są wysyłane lub opublikowane za pośrednictwem przeglądarki, za pośrednictwem protokołu HTTPS, SSL temu, poufne informacje są szyfrowane i chronione przed przechwyceniem.

Aby dowiedzieć się więcej na temat używania certyfikatów protokołu SSL, zobacz [Konfigurowanie protokołu SSL dla aplikacji platformy Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Izolacja sieci i zabezpieczeń za pomocą usługi Azure Service Fabric
Konfigurowanie 3 nodetype zabezpieczonego klastra przy użyciu [szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) jako przykład. Kontrolowanie ruchu przychodzącego i wychodzącego ruchu sieciowego przy użyciu szablonu i sieciowych grup zabezpieczeń.

Szablon ma sieciową grupę zabezpieczeń dla każdej z zestawami skalowania maszyn wirtualnych i jest używane do kontrolowania ruchu do i z tego zestawu. Reguły są domyślnie skonfigurowane, aby zezwolić na cały ruch niezbędne dla usług systemowych i porty aplikacji określonego w szablonie. Przejrzyj te reguły i wprowadź zmiany do własnych potrzeb, w tym dodawanie nowych zasad dla aplikacji.

Aby uzyskać więcej informacji, zobacz [typowe scenariusze sieciowe dla usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurowanie usługi Azure Key Vault dla zabezpieczeń
Usługa Service Fabric używa certyfikatów do uwierzytelniania i szyfrowania do zabezpieczania klastra i jego aplikacji.

Usługa Service Fabric używa certyfikatów X.509, zabezpieczanie klastra oraz udostępnia funkcje zabezpieczeń aplikacji. Możesz użyć usługi Azure Key Vault w celu [zarządzanie certyfikatami](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) klastrów usługi Service Fabric na platformie Azure. Dostawca zasobów platformy Azure, która tworzy klastry pobiera certyfikaty z magazynu kluczy. Dostawca instaluje certyfikatów na maszynach wirtualnych podczas wdrażania klastra na platformie Azure.

Certyfikat relację między [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), klaster usługi Service Fabric i dostawcy zasobów, która używa certyfikatów. Podczas tworzenia klastra informacje dotyczące relacji certyfikatów są przechowywane w magazynie kluczy.

Istnieją dwa podstawowe kroki, aby skonfigurować magazyn kluczy:
1. Utwórz grupę zasobów, specjalnie dla własnego magazynu kluczy.

    Zaleca się umieścić usługi key vault w jego własnej grupie zasobów. Ta akcja ułatwia zapobieganie utracie kluczy i wpisów tajnych, jeśli inne grupy zasobów zostaną usunięte, takie jak storage, obliczeniowych lub grupy, która zawiera Twój klaster. Grupę zasobów, która zawiera magazynu kluczy musi być w tym samym regionie co klaster, który jest używany.

2. Tworzenie magazynu kluczy w nowej grupie zasobów.

    Magazynu kluczy musi być włączona dla wdrożenia. Dostawcy zasobów obliczeniowych można uzyskać certyfikaty z magazynu i zainstalować je na wystąpieniach maszyn wirtualnych.

Aby dowiedzieć się więcej na temat sposobu konfigurowania magazynu kluczy, zobacz [co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji ma reprezentować klaster przypisać użytkowników do ról, które są obsługiwane przez usługę Service Fabric: tylko do odczytu i administratora. Te role można przypisać za pomocą witryny Azure portal.

>[!NOTE]
> Aby uzyskać więcej informacji na temat przy użyciu ról w usłudze Service Fabric, zobacz [opartej na rolach kontrola dostępu dla klientów usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Usługa Azure Service Fabric obsługuje dwa typy kontroli dostępu dla klientów, które są podłączone do [klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administratora i użytkownika. Administrator klastra, można użyć kontroli dostępu, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników. Kontrola dostępu sprawia, że klaster jest bardziej bezpieczne.

## <a name="next-steps"></a>Kolejne kroki

- [Lista kontrolna zabezpieczeń usługi Service Fabric](azure-service-fabric-security-checklist.md)
- Konfigurowanie usługi Service Fabric [środowisko programistyczne](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
