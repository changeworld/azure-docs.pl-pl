---
title: Konfigurowanie projektu platformy Azure przy użyciu konfiguracji z wieloma usługi | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować projekt usługi w chmurze platformy Azure, zmieniając pliki ServiceDefinition.csdef, ServiceConfiguration.Local.cscfg i ServiceConfiguration.Cloud.cscfg.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 919568922ada2d842233ade029a54d474b4a1a0e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Konfigurowanie projektu platformy Azure w programie Visual Studio do wykorzystania wielu konfiguracji usługi

Projekt usługi w chmurze platformy Azure w programie Visual Studio zawiera trzy pliki konfiguracji: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, i `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` zostanie wdrożona na platformie Azure opisano wymagania dotyczące usługi w chmurze i jej role i podaj ustawienia, które są stosowane do wszystkich wystąpień. Ustawienia mogą być odczytywane w czasie wykonywania za pomocą usługi hostingu środowisko uruchomieniowe interfejsu API Azure. Ten plik może zostać zaktualizowana na platformie Azure, tylko po zatrzymaniu usługa w chmurze.
- `ServiceConfiguration.Local.cscfg` i `ServiceConfiguration.Cloud.cscfg` Podaj wartości dla ustawień w definicji pliku i określ liczbę wystąpień, aby uruchomić dla każdej roli. Plik "Lokalnie" zawiera wartości używana podczas debugowania lokalnego. Plik "W chmurze" jest wdrażana na platformie Azure jako `ServiceConfiguration.cscfg` i udostępnia ustawienia w środowisku serwera. Ten plik może zostać zaktualizowana uruchomionej usługi w chmurze na platformie Azure.

Ustawienia konfiguracji są zarządzane i modyfikować w programie Visual Studio za pomocą stron właściwości dla odpowiednich roli (kliknij prawym przyciskiem myszy rolę i wybierz **właściwości**, lub kliknij dwukrotnie roli). Zakres zmiany niezależnie od konfiguracji jest wybierany w **konfiguracji usługi** listy rozwijanej. Właściwości role sieci web i proces roboczy są podobne, chyba że w przypadku, gdy opisane w poniższych sekcjach.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Informacje o podstawowej schematów dla definicji usługi i pliki konfiguracji usługi znajdują się w temacie [csdef schematu XML](cloud-services/schema-csdef-file.md) i [.cscfg schematu XML](cloud-services/schema-cscfg-file.md) artykułów. Aby uzyskać więcej informacji na temat konfiguracji usługi, zobacz [sposobu skonfigurowania usługi w chmurze](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Na stronie konfiguracji

### <a name="service-configuration"></a>Konfiguracja usługi

Wybieranie, które `ServiceConfiguration.*.cscfg` plików mają wpływ zmiany. Domyślnie są wariantów lokalnych i w chmurze i można użyć **Zarządzaj...**  polecenie, aby skopiować, Zmień nazwę i Usuń pliki konfiguracji. Te pliki zostaną dodane do projektu usługi w chmurze i są wyświetlane w **Eksploratora rozwiązań**. Jednak zmiana nazwy lub usunięcie konfiguracji może odbywać się tylko z tego formantu.

### <a name="instances"></a>Wystąpienia

Ustaw **wystąpienia** count — właściwość liczby wystąpień usługi powinno być ono uruchomione dla tej roli.

Ustaw **rozmiar maszyny Wirtualnej** właściwości **dodatkowe małych**, **małych**, **średni**, **duży**, lub **bardzo duży**.  Aby uzyskać więcej informacji, zobacz [rozmiary dla usług w chmurze](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Uruchamianie akcji (tylko role sieci Web)

Ustaw tę właściwość, aby określić programu Visual Studio należy uruchomić przeglądarki sieci web dla punktów końcowych HTTP lub punktów końcowych HTTPS lub zarówno po rozpoczęciu debugowania.

**Punkt końcowy HTTPS** opcja jest dostępna tylko wtedy, gdy punkt końcowy HTTPS został już zdefiniowany dla roli użytkownika. Można zdefiniować punkt końcowy HTTPS **punkty końcowe** strony właściwości.

Jeśli masz już dodany punkt końcowy HTTPS, opcja punkt końcowy HTTPS jest domyślnie włączona i Visual Studio spowoduje uruchomienie przeglądarki dla tego punktu końcowego, po rozpoczęciu debugowania, oprócz przeglądarki dla punktu końcowego HTTP, przy założeniu, że włączone są obie opcje uruchamiania.

### <a name="diagnostics"></a>Diagnostyka

Domyślnie diagnostycznych są włączone dla roli sieci Web. Konto projektu i magazynu usługi chmury Azure są skonfigurowane do korzystania z emulatora magazynu lokalnego. Gdy wszystko będzie gotowe do wdrożenia na platformie Azure, można wybrać przycisk konstruktora (**...** ) zamiast tego użyć usługi Azure storage. Można przenieść danych diagnostycznych na konto magazynu na żądanie lub automatycznie zaplanowanymi interwałami. Aby uzyskać więcej informacji na temat diagnostycznych platformy Azure, zobacz [Włączanie diagnostyki w usług Azure Cloud Services i maszyn wirtualnych](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Strona Ustawienia

Na **ustawienia** strony, można dodać ustawień do konfiguracji jako pary nazwa wartość. Kodu działającego w roli można odczytać wartości ustawień konfiguracji w czasie wykonywania za pomocą klasy udostępniane przez [biblioteki zarządzane Azure](http://go.microsoft.com/fwlink?LinkID=171026), w szczególności [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metody.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>Konfigurowanie ciągu połączenia dla konta magazynu

Ciąg połączenia jest ustawienie, które zawiera informacje o połączenia i uwierzytelniania dla emulatora magazynu lub na koncie magazynu platformy Azure. Zawsze, gdy kod w roli uzyskuje dostęp do usługi Azure storage (obiekty BLOB, kolejek lub tabele), wymaga parametrów połączenia.

> [!Note]
> Ciąg połączenia dla konta magazynu platformy Azure musi mieć format określonych (zobacz [skonfigurować parametry połączenia magazynu Azure](storage/common/storage-configure-connection-string.md)).

Ciąg połączenia używany Magazyn lokalny, zgodnie z potrzebami, a następnie ustaw konto magazynu platformy Azure podczas wdrażania aplikacji można ustawić usługę w chmurze. Nie można poprawnie ustawić parametry połączenia mogą powodować roli użytkownika nie można uruchomić lub mieć inicjowania zajęty i zatrzymywanie.

Aby utworzyć parametry połączenia, wybierz **Dodaj ustawienie** i ustaw **typu** "Ciąg połączenia".

Ciągi nowego lub istniejącego połączenia, wybierz **...** * po prawej stronie **wartość** pole, aby otworzyć **utworzyć parametry połączenia magazynu** okna dialogowego:

1. W obszarze **łączyć się przy użyciu**, wybierz **subskrypcji** opcję, aby wybrać konto magazynu z subskrypcji. Visual Studio pobiera następnie automatycznie za pomocą poświadczeń konta magazynu `.publishsettings` pliku.
1. Wybieranie **ręcznie wprowadzić poświadczenia** pozwala określić nazwę konta i klucza bezpośrednio przy użyciu informacji z portalu Azure. Aby skopiować klucz konta:. Przejdź do konta magazynu Azure, portalu i wybierz pozycję **zarządzanie kluczami**.
    2. Aby skopiować klucz konta, przejdź do konta magazynu w portalu Azure, wybierz opcję **Ustawienia > klucze dostępu**, następnie użyj przycisku kopiowania, aby skopiuj podstawowy klucz dostępu do Schowka.
1. Wybierz jedną z opcji połączenia. **Określ niestandardowe punkty końcowe** prosi o określenie określonych adresów URL dla obiektów blob, tabel i kolejek. Niestandardowe punkty końcowe umożliwiają używanie [domen niestandardowych](storage/blobs/storage-custom-domain-name.md) i kontrolować dostęp do bardziej dokładnie. Zobacz [Konfiguracja parametrów połączenia usługi Azure Storage](./storage/common/storage-configure-connection-string.md).
1. Wybierz **OK**, następnie **Plik > Zapisz** można zaktualizować konfiguracji z użyciem nowego ciągu połączenia.

Ponownie podczas publikowania aplikacji na platformie Azure, wybierz konfigurację usługi zawierającej konto magazynu Azure w ciągu połączenia. Po opublikowaniu aplikacji, sprawdź, czy aplikacja działa zgodnie z oczekiwaniami w odniesieniu do usług Azure storage.

Aby uzyskać więcej informacji o sposobie aktualizowania konfiguracji usługi, zobacz sekcję [Zarządzanie parametry połączenia dla konta magazynu](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Strona punkty końcowe

Rola sieci web zwykle ma jeden punkt końcowy HTTP na porcie 80. Z drugiej strony roli proces roboczy może mieć dowolną liczbę punktów końcowych HTTP, HTTPS lub TCP. Punkty końcowe można wejściowych punktów końcowych, które są dostępne dla klientów zewnętrznych, lub wewnętrznych punktów końcowych, które są dostępne dla innych ról uruchomionych w usłudze.

- Aby udostępnić punkt końcowy HTTP klientami zewnętrznymi i przeglądarki sieci Web, Zmień typ punktu końcowego jako danych wejściowych i określ nazwę i numer portu publicznego.
- Aby udostępnić punkt końcowy HTTPS klientów zewnętrznych i przeglądarki sieci Web, należy zmienić typ punktu końcowego na **wejściowych**, a następnie określ nazwę, numeru portu publicznego i nazwę certyfikatu zarządzania. Certyfikat musi także definiować na **certyfikaty** strony właściwości, aby można było określić certyfikat zarządzania. 
- Aby udostępnić punkt końcowy dla wewnętrznego dostępu przez inne role w usłudze w chmurze, Zmień typ punktu końcowego do wewnętrznego i określ nazwę i możliwe prywatnych portów dla tego punktu końcowego.

## <a name="local-storage-page"></a>Strona magazynu lokalnego

Można użyć **Magazyn lokalny** strony właściwości do rezerwowania co najmniej jeden zasób lokalny magazyn dla roli. Zasób Magazyn lokalny jest zastrzeżony katalogu w systemie plików maszyny wirtualnej platformy Azure, w którym jest uruchomione wystąpienie roli.

## <a name="certificates-page"></a>Strona Certyfikaty

**Certyfikaty** strony właściwości dodaje informacje o certyfikatach do konfiguracji usługi. Należy pamiętać, że certyfikaty nie są dostarczane z usługą; Certyfikaty musisz je oddzielnie na platformie Azure za pośrednictwem [portalu Azure](http://portal.azure.com).

Dodawanie certyfikatu w tym miejscu dodaje informacje o certyfikatach do konfiguracji usługi. Certyfikaty nie są dostarczane z usługą; należy przekazać certyfikaty z osobna za pomocą portalu Azure.

Aby skojarzyć certyfikatu z roli użytkownika, należy podać nazwę certyfikatu. Nazwa ta będzie używana do odwoływania się do certyfikatu podczas konfigurowania punktu końcowego HTTPS **punkty końcowe** strony. Następnie określ, czy magazyn certyfikatów jest **komputera lokalnego** lub **bieżącego użytkownika** i nazwę magazynu. Na koniec wprowadź odcisk palca certyfikatu. Jeśli certyfikat znajduje się w bieżącym User\Personal magazynu (Mój), możesz wprowadzić odcisk palca certyfikatu, wybierając certyfikat z listy wypełnione. Jeśli znajduje się ona w innej lokalizacji, należy ręcznie wprowadź wartość odcisku palca.

Po dodaniu certyfikatu z magazynu certyfikatów, wszelkie certyfikaty pośrednie są automatycznie dodawane do ustawienia konfiguracji. Ponadto te certyfikaty pośrednie, należy przekazać do platformy Azure, aby poprawnie skonfigurować usługi dla protokołu SSL.

Wszystkie certyfikaty zarządzania skojarzonych z usługą dotyczą usługi tylko wtedy, gdy jest uruchomiona w chmurze. Gdy usługa działa w środowisku programistycznym lokalnego, używa to standardowy certyfikat, który jest zarządzany przez emulator obliczeń.
