---
title: Więcej informacji na temat zabezpieczeń aplikacji usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Omówienie sposobu bezpiecznego uruchamiania aplikacji mikrousług w usłudze Service Fabric. Informacje o sposobie uruchamiania usług i uruchamiania skryptu w ramach kont zabezpieczeń, uwierzytelniania i autoryzacji użytkowników, Zarządzanie wpisami tajnymi aplikacji, bezpieczna komunikacja usług, użyj bramy interfejsu API i bezpiecznych aplikacji danych magazynowanych.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: aljo
ms.openlocfilehash: b4d3699c0327bb2771a358d3e3c2921bdc39ee5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621566"
---
# <a name="service-fabric-application-and-service-security"></a>Aplikacja usługi Service Fabric i usługi zabezpieczeń
Architektura mikrousług może przynieść [wiele korzyści](service-fabric-overview-microservices.md). Zarządzanie zabezpieczeniami mikrousług, jest jednak żądania i inne niż zarządzanie zabezpieczeniami tradycyjne aplikacje monolityczne. 

Za pomocą monolitu której zwykle działa aplikacja na jeden lub więcej serwerów w sieci i jest łatwiejszy do zidentyfikowania narażone porty i interfejsy API i adres IP. Często występuje jeden obwód lub granic i jedną bazę danych do ochrony. W przypadku naruszenia zabezpieczeń ze względu na naruszenie zabezpieczeń lub atak tego systemu, jest prawdopodobne, że wszystkie elementy w ramach systemu będą dostępne dla osoby atakującej. Korzystanie z mikrousług system jest bardziej złożone.  Usługi są używane zdecentralizowane i rozproszone na wielu hostach i migracji z hosta.  Za pomocą właściwych zabezpieczeń ograniczysz uprawnienia, które osoba atakująca może uzyskać i ilość danych dostępnych w jednym ataku przez wysłanie jednej usługi.  Komunikacja nie jest wewnętrzny, ale się stanie, za pośrednictwem sieci, a istnieje wiele ujawnionych portów i interakcje między usługami. Wiedząc, jakie są interakcje te usługi i gdy wystąpią ma podstawowe znaczenie dla zabezpieczeń aplikacji.

Ten artykuł nie jest przewodnik dotyczący zabezpieczeń mikrousług, dostępnych jest wiele takich zasobów dostępnych w trybie online, ale w tym artykule opisano jak różne aspekty zabezpieczeń może się odbywać w usłudze Service Fabric.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Często jest to niezbędne do zasobów i interfejsach API udostępnianych przez usługę ograniczone do niektórych Zaufani użytkownicy lub klienci. Uwierzytelnianie to proces niezawodnie ustalenia tożsamości użytkownika.  Autoryzacja to proces, który sprawia, że interfejsy API i usług dostępne dla niektórych uwierzytelnieni użytkownicy, ale innych nie.

### <a name="authentication"></a>Authentication
Pierwszym krokiem do podejmowania decyzji dotyczących zaufania poziom interfejsu API to uwierzytelnianie. Uwierzytelnianie to proces niezawodnie ustalenia tożsamości użytkownika.  W scenariuszach mikrousług uwierzytelniania jest zazwyczaj obsługiwana centralnie. Jeśli używasz bramy interfejsu API możesz [odciążania uwierzytelniania](/azure/architecture/patterns/gateway-offloading) do bramy. Jeśli używasz tego podejścia, upewnij się, że poszczególnych usług nie można połączyć bezpośrednio (bez bramy interfejsu API), chyba, że dodatkowe zabezpieczenia są używane do uwierzytelniania komunikatów tego, czy pochodzą one od bramą lub nie.

Jeśli usługi są dostępne bezpośrednio, usługi uwierzytelniania, takich jak Azure Active Directory lub mikrousług dedykowanych uwierzytelniania, pełniący funkcję zabezpieczeń, usługa tokenów (STS) może być używane do uwierzytelniania użytkowników. Usługi za pomocą tokenów zabezpieczających lub pliki cookie są współużytkowane decyzji dotyczących zaufania. 

Dla platformy ASP.NET Core przy użyciu podstawowego mechanizmu [uwierzytelniania użytkowników](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) jest systemu członkostwa programu ASP.NET Core Identity. Tożsamości platformy ASP.NET Core przechowuje informacje o użytkowniku (w tym informacje logowania, ról i oświadczenia) w magazynie danych skonfigurowane przez dewelopera. Tożsamość platformy ASP.NET Core obsługuje uwierzytelnianie dwuskładnikowe.  Zewnętrzni dostawcy uwierzytelniania są również obsługiwane, dzięki czemu użytkownicy mogą zalogować się przy użyciu istniejące procesy uwierzytelniania od dostawców, takich jak Microsoft, Google, Facebook lub Twitter. 

### <a name="authorization"></a>Autoryzacja
Po uwierzytelnieniu usługi, należy autoryzować dostęp użytkownika lub określić, jakie użytkownik będzie mógł robić. Ten proces umożliwia usłudze się interfejsów API, niektórzy użytkownicy uwierzytelnieni, ale nie dla wszystkich. Autoryzacja jest prostopadły i niezależna od uwierzytelniania, które polega na upewnieniu się, kim jest użytkownik. Uwierzytelnianie może utworzyć jedną lub więcej tożsamości dla bieżącego użytkownika.

[Autoryzacji platformy ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) mogą być oparte na rolach użytkowników lub na podstawie zasad niestandardowych, która może obejmować kontrolę opartą na oświadczeniach lub inne algorytmy heurystyczne.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Ogranicz i zabezpieczyć dostęp przy użyciu bramy interfejsu API
Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. [Bramy interfejsu API](/azure/architecture/microservices/gateway) znajduje się pomiędzy klientów i usług i jest punktem wejścia do wszystkich usług, które dostarcza aplikację. Działa ona jako zwrotny serwer proxy kierowania żądań od klientów do usługi. Go, że również wykonywać różne zadania przekrojowe, takich jak uwierzytelnianie i autoryzacja, kończenie żądań SSL i ograniczanie szybkości. Jeśli nie wdrożono bramę, klienci muszą wysyłać żądania bezpośrednio do usługi frontonu.

W usłudze Service Fabric bramą może być dowolna usługa bezstanowa takich jak [aplikacji platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), lub inna usługa przeznaczona dla ruchu przychodzącego, takich jak [Traefik](https://docs.traefik.io/), [usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/), lub [usługi Azure API Management](https://docs.microsoft.com/azure/api-management).

Usługa API Management, integruje się bezpośrednio z usługą Service Fabric, umożliwiające publikowanie interfejsów API bogaty zestaw reguł routingu w celu usługi Service Fabric zaplecza.  Możesz bezpieczny dostęp do usług zaplecza, zapobieganie atakom DOS przy użyciu ograniczania przepustowości lub sprawdź interfejsu API kluczy, tokenów JWT tokeny, certyfikaty i inne poświadczenia. Aby dowiedzieć się więcej, przeczytaj [usługi Service Fabric za pomocą usługi Azure API Management overview](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Zarządzanie wpisami tajnymi aplikacji
Wpisy tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu. W tym artykule używa usługi Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Jednak *przy użyciu* wpisów tajnych aplikacji w chmurze jest niezależne od platformy do umożliwienia aplikacji można wdrożyć w klastrze, hostowanych w dowolnym miejscu.

Zalecanym sposobem zarządzania ustawienia konfiguracji usługi jest za pośrednictwem [usługi pakiety konfiguracji][config-package]. Pakiety konfiguracji są wersjonowane i nadaje się do aktualizacji za pomocą zarządzanych uaktualnienia stopniowe przy użyciu wycofywania sprawdzania kondycji i automatyczny. Jest to preferowany do globalnej konfiguracji, ponieważ zmniejsza ryzyko awarii usługi global service. Zaszyfrowane klucze tajne są żaden wyjątek. Usługa Service Fabric ma wbudowane funkcje szyfrowania i odszyfrowywania wartości w pliku konfiguracji pakietu Settings.xml przy użyciu certyfikatu szyfrowania.

Na poniższym diagramie przedstawiono przepływ podstawowy do zarządzania wpisami tajnymi w aplikacji usługi Service Fabric:

![Omówienie zarządzania wpisami tajnymi][overview]

Istnieją cztery główne kroki, w tym przepływie:

1. Uzyskaj certyfikat Szyfrowanie danych.
2. Zainstaluj certyfikat w klastrze.
3. Szyfrowanie klucza tajnego wartości podczas wdrażania aplikacji za pomocą certyfikatu i wstawić je do pliku konfiguracji Settings.xml usługi.
4. Odczytać zaszyfrowane wartości poza Settings.xml odszyfrowywania za pomocą tego samego certyfikatu szyfrowania. 

[Usługa Azure Key Vault] [ key-vault-get-started] jest używany tutaj jako lokalizację bezpieczne przechowywanie certyfikatów i sposobem uzyskania certyfikatów zainstalowanych w klastrach usługi Service Fabric na platformie Azure. Jeśli nie są wdrażane na platformie Azure, nie należy używać usługi Key Vault do zarządzania wpisami tajnymi w aplikacji usługi Service Fabric.

Aby uzyskać przykład, zobacz [Zarządzanie wpisami tajnymi aplikacji](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpieczenia środowiska macierzystego
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacje, które są uruchomione w klastrze, w ramach różnych kont użytkowników. Usługa Service Fabric pomaga również w zabezpieczenia zasobów używanych przez aplikacje w czasie wdrażania na kontach użytkowników — na przykład, pliki, katalogi i certyfikatów. Dzięki temu uruchomionych aplikacji, nawet w środowisku współdzielonym hostowanej bardziej bezpieczne od siebie nawzajem.

Manifest aplikacji deklaruje, że podmiotów zabezpieczeń (użytkownikami i grupami) wymagane uruchamianie usług i bezpiecznych zasobów.  Te jednostki są określone w zasadach, na przykład Uruchom jako, powiązania punktu końcowego, zabezpieczeń w pakiecie udostępniania lub zasad dostępu zabezpieczeń.  Zasady są następnie stosowane do zasobów usługi w **ServiceManifestImport** części manifestu aplikacji.

Podczas deklarowania podmiotów zabezpieczeń, można również zdefiniować i utworzyć grupy użytkowników, tak aby co najmniej jednego użytkownika można dodać do każdej grupy, aby zarządzać razem. Jest to przydatne, gdy jest wielu użytkowników do innej usługi, punkty wejścia i muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy.

Domyślnie aplikacje usługi Service Fabric uruchamiana na koncie, zgodną z procesu Fabric.exe. Usługa Service Fabric udostępnia także możliwości uruchamiania aplikacji w ramach konta użytkownika lokalnego lub konta systemu lokalnego, która jest określona w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [uruchamianie usługi jako konto użytkownika lokalnego lub konta system lokalny](service-fabric-application-runas-security.md).  Możesz również [uruchamianie skryptu uruchamiania usługi jako lokalne konto użytkownika lub systemu](service-fabric-run-script-at-service-startup.md).

Jeśli korzystasz z usługi Service Fabric w klastrze autonomicznego Windows, można uruchomić usługi w ramach [kont domeny usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpieczanie kontenerów
Usługa Service Fabric udostępnia mechanizm dla usług w kontenerze na dostęp do certyfikatu, który jest zainstalowany na węzłach w klastrze Windows lub Linux (w wersji 5.7 lub nowszej). Ten certyfikat PFX może służyć do uwierzytelniania aplikacji lub usługi albo bezpiecznej komunikacji z innymi usługami. Aby uzyskać więcej informacji, zobacz [importowania certyfikatu w kontenerze](service-fabric-securing-containers.md).

Ponadto Usługa Service Fabric obsługuje również gMSA (konta usług zarządzane grupy) kontenery Windows. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta gMSA dla kontenerów Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Bezpieczna komunikacja usług
W usłudze Service Fabric usługa jest uruchamiana gdzieś w klastrze usługi Service Fabric, zazwyczaj dystrybuowane w wielu maszyn wirtualnych. Usługa Service Fabric udostępnia kilka opcji do zabezpieczania komunikacji usługi.

Można włączyć punktów końcowych HTTPS w swojej [platformy ASP.NET Core lub Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) usług sieci web.

Można nawiązać bezpiecznego połączenia między zwrotny serwer proxy i usług, umożliwiając w ten sposób bezpiecznego kanału typu end to end. Łączenie się z usługami bezpieczne jest obsługiwana tylko wtedy, gdy zwrotny serwer proxy jest skonfigurowana do nasłuchiwania przy użyciu protokołu HTTPS. Aby uzyskać informacje na temat konfigurowania zwrotny serwer proxy, należy przeczytać [zwrotny serwer proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy.md).  [Łączenie z usługą bezpieczny](service-fabric-reverseproxy-configure-secure-communication.md) opisuje metodę ustanawiania bezpiecznego połączenia między zwrotny serwer proxy i usług.

Struktura aplikacji usług Reliable Services zawiera kilka wbudowanych komunikacji stosy i narzędzia, których można użyć w celu zwiększenia bezpieczeństwa. Dowiedz się, jak zwiększyć bezpieczeństwo, podczas korzystania z komunikacji zdalnej usługi (w [ C# ](service-fabric-reliable-services-secure-communication.md) lub [Java](service-fabric-reliable-services-secure-communication-java.md)) lub za pomocą [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Szyfrowanie nieużywanych danych aplikacji
Każdy [typ węzła](service-fabric-cluster-nodetypes.md) w klastrze usługi Service Fabric działających na platformie Azure jest wspierana przez [zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Za pomocą szablonu usługi Azure Resource Manager możesz dołączać dyski z danymi do zestawów skalowania tworzących klaster usługi Service Fabric.  Jeśli usługi zapisać danych do dołączonego dysku danych, możesz to zrobić [szyfrowania tych dysków z danymi](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) Aby chronić dane Twoich aplikacji.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Uruchom skrypt instalacji podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Więcej informacji na temat zabezpieczeń klastra](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png