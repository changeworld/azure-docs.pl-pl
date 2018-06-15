---
title: Więcej informacji na temat zabezpieczeń aplikacji sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Przegląd sposobu bezpiecznego uruchamiania mikrousług aplikacji w sieci szkieletowej usług. Informacje o sposobie uruchamiania usług i uruchamiania skryptu w obszarze konta zabezpieczeń, uwierzytelniania i autoryzacji użytkowników, zarządzać hasła aplikacji, bezpieczna komunikacja usługi, użyj interfejsu API bramy oraz danych aplikacji bezpiecznego magazynowane.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: fa6d46186ad833b68e60c24f742d210b7845759a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207914"
---
# <a name="service-fabric-application-and-service-security"></a>Sieć szkieletowa usług aplikacji i usług zabezpieczeń
Architektura mikrousług można przełączyć [wiele korzyści](service-fabric-overview-microservices.md). Zarządzanie zabezpieczeniami elementu mikrousług, jest jednak żądanie oraz inne niż zarządzanie zabezpieczeniami tradycyjne aplikacje wbudowanymi. 

Monolityczna aplikacji zwykle działa na co najmniej jednym serwerze w sieci i łatwiej zidentyfikować dostępnego portów i adresów IP i interfejsów API. Często istnieje jeden obwodowej lub granic i jedną bazę danych do ochrony. Naruszenie tego systemu z powodu naruszenia zabezpieczeń lub atak istnieje prawdopodobieństwo, że wszystkie elementy w ramach systemu będą dostępne dla osoby atakującej. Z mikrousług system jest bardziej złożony.  Usługi są używane zdecentralizowane i rozproszone na wielu hostach i przeprowadzić migrację między hostami.  Z właściwe zabezpieczenia można ograniczyć uprawnienia, które osoba atakująca może uzyskać i ilość danych, które są dostępne w jednym ataku naruszenie jedna usługa.  Komunikacja nie jest wewnętrznych, ale się stanie w sieci, a istnieje wiele portów narażonych i interakcje między usługami. Interakcje te usługi są i gdy wystąpią ma podstawowe znaczenie dla zabezpieczeń aplikacji.

W tym artykule nie jest przewodnik dotyczący zabezpieczeń mikrousług, dostępnych wiele takich zasobów online, ale zawiera opis różnych aspektów zabezpieczeń można wykonywać w sieci szkieletowej usług.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Często jest to niezbędne do zasobów i interfejsach API udostępnianych przez usługę ograniczone do niektórych Zaufani użytkownicy lub klienci. Uwierzytelnianie to proces niezawodnie ustalenia tożsamości użytkownika.  Autoryzacja to proces, który udostępnia interfejsy API lub usługi dostępne dla niektórych uwierzytelnieni użytkownicy, a innych nie.

### <a name="authentication"></a>Authentication
Pierwszym krokiem do podejmowania decyzji dotyczących zaufania poziom interfejsu API jest uwierzytelnianie. Uwierzytelnianie to proces niezawodnie ustalenia tożsamości użytkownika.  W scenariuszach mikrousługi uwierzytelniania zwykle odbywa się centralnie. Jeśli korzystasz z bramy usługi interfejsu API, możesz [odciążania uwierzytelniania](/azure/architecture/patterns/gateway-offloading) do bramy. Jeśli używasz tej metody, upewnij się, że poszczególnych usług nie można połączyć bezpośrednio (bez bram interfejsu API), chyba że dodatkowe zabezpieczenia w celu uwierzytelniania wiadomości czy pochodzą z bramy lub nie.

Jeśli usługi są dostępne bezpośrednio, usługi uwierzytelniania, takich jak Azure Active Directory lub mikrousługi dedykowanych uwierzytelniania, działając jako zabezpieczeń usługi tokenów (STS) może być używane do uwierzytelniania użytkowników. Decyzje zaufania są wspólne dla usług z tokenów zabezpieczających lub plików cookie. 

Dla platformy ASP.NET Core podstawowego mechanizmu [uwierzytelniania użytkowników](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) jest systemu członkostwa ASP.NET Core Identity. Tożsamość platformy ASP.NET Core przechowuje informacje o użytkowniku (w tym informacje logowania, ról i oświadczeń) w magazynie danych skonfigurowane przez dewelopera. Tożsamość platformy ASP.NET Core obsługuje uwierzytelnianie dwuskładnikowe.  Zewnętrzni dostawcy uwierzytelniania są również obsługiwane, aby użytkownicy mogą logować się przy użyciu istniejące procesy uwierzytelniania od dostawców, takich jak Microsoft, Google, Facebook i Twitter. 

### <a name="authorization"></a>Autoryzacja
Po uwierzytelnieniu usługi należy do autoryzowania dostępu do użytkownika lub Sprawdzanie, jakie użytkownika jest w stanie wykonać. Ten proces umożliwia usłudze upewnij interfejsów API, niektóre uwierzytelnionych użytkowników, ale nie do wszystkich. Autoryzacja jest prostopadły i niezależnie od uwierzytelniania, które polega na upewnieniu się, kto jest użytkownikiem. Uwierzytelnianie może utworzyć co najmniej jeden tożsamości dla bieżącego użytkownika.

[Autoryzacji platformy ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) mogą być wykonywane w oparciu o role użytkowników lub na podstawie niestandardowych zasad, która może obejmować sprawdzania oświadczeń lub inne algorytmy heurystyczne.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Ogranicz i bezpieczny dostęp przy użyciu bramy interfejsu API
Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. [Bramy interfejsu API](/azure/architecture/microservices/gateway) znajduje się pomiędzy klientów i usług i jest punktem wejścia do wszystkich usług, które są dostarczane przez aplikację. Działa ona jako zwrotny serwer proxy, routingu żądań od klientów do usług. Go mogą również wykonywać różne zadania kompleksowymi uwierzytelniania i autoryzacji, kończenia żądań SSL oraz limitów szybkości. Jeśli nie można wdrożyć bramę, klienci muszą wysyłać żądania bezpośrednio do usługi frontonu.

W sieci szkieletowej usług, brama może być dowolnym usługi bezstanowej takich jak [aplikacji platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), lub inna usługa przeznaczone dla ruch przychodzący, takich jak [Træfik](https://docs.traefik.io/), [usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Centrum IoT](https://docs.microsoft.com/azure/iot-hub/), lub [usługi Azure API Management](https://docs.microsoft.com/azure/api-management).

Zarządzanie interfejsami API integruje się bezpośrednio z usługi Service Fabric, co umożliwia publikowanie interfejsów API za pomocą bogaty zestaw reguł routingu do usług sieci szkieletowej usług zaplecza.  Można bezpieczny dostęp do usługi zaplecza, zapobiegać atakom DOS przy użyciu ograniczania lub sprawdź interfejsu API klucze, JWT tokeny, certyfikaty i inne poświadczenia. Aby dowiedzieć się więcej, przeczytaj [sieci szkieletowej usług Azure API Management Przegląd](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Zarządzanie wpisami tajnymi aplikacji
Klucze tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu. W tym artykule używa usługi Azure Key Vault do zarządzania kluczy i kluczy tajnych. Jednak *przy użyciu* kluczy tajnych w aplikacji w chmurze jest niezależny od platformy aby umożliwić aplikacjom można wdrożyć w klastrze hostowane w dowolnym miejscu.

Zalecanym sposobem zarządzania ustawieniami konfiguracji usługi jest za pośrednictwem [usługi pakiety konfiguracji][config-package]. Pakiety konfiguracji są kontrolą wersji i nadaje się do aktualizacji za pomocą zarządzanego stopniowe z sprawdzania kondycji i automatycznego wycofywania. Jest to preferowana względem konfigurację globalną zmniejsza ryzyko awarii usługi globalne. Zaszyfrowanych kluczy tajnych są żaden wyjątek. Sieć szkieletowa usług ma wbudowane funkcje szyfrowania i odszyfrowywania wartości w pliku Settings.xml pakietu konfiguracji przy użyciu certyfikatu szyfrowania.

Na poniższym diagramie przedstawiono podstawowy przepływ zarządzania tajnych w aplikacji sieci szkieletowej usług:

![Zarządzanie tajne — omówienie][overview]

W tym przepływie istnieją cztery główne kroki:

1. Uzyskaj certyfikat Szyfrowanie danych.
2. Zainstaluj certyfikat w klastrze.
3. Szyfrowanie tajny wartości podczas wdrażania aplikacji przy użyciu certyfikatu i wstawić je do pliku konfiguracji Settings.xml usługi.
4. Odszyfrowywanie przy użyciu tego samego certyfikatu szyfrowanie odczytywać zaszyfrowanych wartości poza Settings.xml. 

[Usługa Azure Key Vault] [ key-vault-get-started] jest tu używany jako lokalizację bezpiecznego magazynu certyfikatów, a sposobem uzyskania certyfikatów zainstalowanych na klastrów sieci szkieletowej usług platformy Azure. Jeśli nie są wdrażane na platformie Azure, nie trzeba zarządzać kluczy tajnych w aplikacji sieci szkieletowej usług za pomocą usługi Key Vault.

Na przykład zobacz [Zarządzanie klucze tajne aplikacji](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Bezpieczne środowisko hostingu
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacji uruchomionych w klastrze, w obszarze konta innego użytkownika. Zabezpieczanie zasobów, które są używane przez aplikacje w czasie wdrażania na kontach użytkowników — na przykład, plików, katalogów i certyfikatów pomaga również w sieci szkieletowej usług. Dzięki temu uruchamianie aplikacji, nawet w środowisku hostowanej udostępnionego bardziej bezpieczne od siebie nawzajem.

Manifest aplikacji deklaruje podmiotów zabezpieczeń (użytkownikami i grupami) wymagane uruchamianie usług i zabezpieczania zasobów.  Te zabezpieczeń podmiotów zabezpieczeń są przywoływane w zasadach, na przykład Uruchom jako, punkt końcowy powiązanie, pakiet udostępnianie lub zasad zabezpieczeń dostępu.  Zasady zostaną zastosowane do usługi zasobów w **ServiceManifestImport** części dla manifest aplikacji.

Przy deklarowaniu podmiotów zabezpieczeń, można również zdefiniować i Utwórz grupy użytkowników, dzięki czemu można dodać co najmniej jednego użytkownika do każdej grupy do wspólnego zarządzania. Jest to przydatne, jeśli jest wielu użytkowników do innej usługi, punkty wejścia i muszą mieć niektórych typowych uprawnień, które są dostępne na poziomie grupy.

Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, działającą proces Fabric.exe. Usługi sieć szkieletowa dostępne są także możliwość uruchamiania aplikacji w ramach konta użytkownika lokalnego lub konta system lokalny, które jest określone w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [uruchamianie usługi jako konto użytkownika lokalnego lub konta system lokalny](service-fabric-application-runas-security.md).  Możesz również [uruchomienia skryptu uruchamiania usługi jako konto użytkownika lub systemu lokalnego](service-fabric-run-script-at-service-startup.md).

Po uruchomieniu usługi Service Fabric w klastrze autonomicznego Windows, można uruchomić usługi w ramach [kont domen usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpieczanie kontenerów
Sieć szkieletowa usług udostępnia mechanizm dla usług wewnątrz kontenera można uzyskać dostępu do certyfikatu, który jest zainstalowany na węzłach w klastrze systemu Windows lub Linux (w wersji 5.7 lub nowszej). Ten certyfikat PFX mogą służyć do uwierzytelniania aplikacji lub usługi albo bezpiecznej komunikacji z innymi usługami. Aby uzyskać więcej informacji, zobacz [zaimportuj certyfikat do kontenera](service-fabric-securing-containers.md).

Ponadto sieci szkieletowej usług również obsługuje gMSA (kont usług zarządzanych grupy) dla systemu Windows kontenerów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie gMSA kontenerów Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Zabezpieczenia komunikacji usługi
W sieci szkieletowej usług Usługa gdzieś działa w klastrze usługi sieć szkieletowa, zazwyczaj są rozproszone na wielu maszyn wirtualnych. Sieć szkieletowa usług zapewnia kilka opcji zabezpieczania komunikacji usługi.

Można włączyć punktów końcowych HTTPS w Twojej [platformy ASP.NET Core lub Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) usług sieci web.

Można ustanowić bezpiecznego połączenia między zwrotnego serwera proxy i usług, udostępniając kompleksowe bezpiecznego kanału. Łączenie z usługami bezpiecznego jest obsługiwana tylko wtedy, gdy zwrotnego serwera proxy jest skonfigurowane do nasłuchiwania protokołu HTTPS. Aby uzyskać informacje na temat konfigurowania zwrotnego serwera proxy, należy przeczytać [odwrotny serwer proxy w sieci szkieletowej usług Azure](service-fabric-reverseproxy.md).  [Połączyć się z usługą bezpieczny](service-fabric-reverseproxy-configure-secure-communication.md) opisuje metodę ustanawiania bezpiecznego połączenia między zwrotnego serwera proxy i usług.

Struktura aplikacji niezawodne usługi zapewnia kilka stosy wbudowane komunikacji i narzędzi, których można użyć w celu poprawy bezpieczeństwa. Dowiedz się, jak poprawić bezpieczeństwo podczas korzystania z komunikacji zdalnej usługi (w [C#](service-fabric-reliable-services-secure-communication.md) lub [Java](service-fabric-reliable-services-secure-communication-java.md)) lub przy użyciu [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Szyfrowanie danych aplikacji przechowywanych
Każdy [typu węzła](service-fabric-cluster-nodetypes.md) w klastrze usługi sieć szkieletowa usług działających na platformie Azure nie jest obsługiwana przez [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Za pomocą szablonu usługi Azure Resource Manager możesz dołączać dyski z danymi do zestawów skalowania tworzących klaster usługi Service Fabric.  Jeśli usługi zapisać danych na dysku w dołączonych danych, możesz [szyfrowania tych dysków danych](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) do ochrony danych aplikacji.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Uruchom skrypt instalacyjny podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Więcej informacji na temat zabezpieczeń klastra](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png