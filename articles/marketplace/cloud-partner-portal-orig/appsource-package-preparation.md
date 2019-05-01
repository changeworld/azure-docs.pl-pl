---
title: Przygotowanie pakietów usługi AppSource | Portal Azure Marketplace
description: Explanaion w jak przygotować i skompilować pakiety w usłudze AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ff822e87bfec5daa161172c0d47975eb06cc2808
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935643"
---
# <a name="appsource-package-preparation"></a>Przygotowanie pakietów usługi AppSource

Oprócz pliku solution.zip należy **pakietu usługi AppSource**. Jest to plik zip, który zawiera wszystkie zasoby potrzebne do automatyzacji procesu wdrażania rozwiązania do klientów CRM środowiska. **Pakietu usługi AppSource** zawiera następujące składniki

* Pakiet dla narzędzia do wdrażania pakietów
* **Content_Types.XML** pliku z zasobami, możesz użyć
* plik XML z danymi specyficzny dla aplikacji
* logo 32 x 32, które będą wyświetlane ze swojej listy w Centrum administracyjnym
* Postanowienia licencyjne i zasady zachowania poufności informacji

Poniższe kroki są pomocne podczas tworzenia pakietu usługi AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Tworzenie pakietu dla narzędzia do wdrażania pakietów

Pakiet dla narzędzia do wdrażania pakietów jest jedną z części pakietu usługi AppSource.

Aby utworzyć pakiet dla narzędzia do wdrażania pakietów, użyj poniższych instrukcji: [ https://msdn.microsoft.com/library/dn688182.aspx ](https://msdn.microsoft.com/library/dn688182.aspx). Po zakończeniu pakietu będzie składać się z poniższych zasobów:

1. Folder pakietu: zawiera wszystkie rozwiązania, dane konfiguracji, pliki proste i zawartość dla pakietu. _Uwaga: W poniższym przykładzie firma Microsoft jest założenie, że folder pakietu jest nazywany "PkgFolder"_
2. dll: Zestaw zawiera kod niestandardowy dla pakietu. _Uwaga: W poniższym przykładzie firma Microsoft jest założenie, że ten plik jest nazywany "MicrosoftSample.dll."_

Teraz musisz utworzyć plik o nazwie "**Content_Types.xml**" ten plik znajduje się lista wszystkich rozszerzeń zasobów, które są częścią pakietu. Poniżej przedstawiono przykładowy kod do pliku.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Ostatnim krokiem jest następujące informacje w jednym pliku zip. Wywołaj go **package.zip**. Będzie ona zawierać

1. PkgFolder (w tym wszystkie elementy w folderze)
2. biblioteki dll
3. **Content_Types.XML**

Kroki umożliwiające utworzenie package.zip:

1. Umieść folderu pakietu **Content_Types.xml** plików i PackageName.dll w jednym katalogu.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Wybierz wszystkie elementy w folderze, kliknij prawym przyciskiem myszy i wybierz folder, Wyślij do skompresowany (zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Zmień nazwę na package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Tworzenie pakietu usługi AppSource

Pakiet usługi AppSource wymaga kilku dodatkowych plików.

1. jpg (rozdzielczość 32 x 32)
2. HTML (pliku w formacie HTML)
3. **Content_Types.XML** (tak samo jak powyżej)
4. xml

Poniżej przedstawiono przykładowy kod, aby uzyskać plik input.xml. Zobacz definicji w poniższej tabeli.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Gdzie:**

|Pole|Szczegóły|
|---|---|
|ProviderName|Kto jest rozwiązanie pochodzące z. Jeśli zespół firmy Microsoft, powinna to być firmy Microsoft.|
|PackageFile |Pakiet wdrażania zasobów zip wraz z zawartością\_types.xml pliku. Ten plik zip może zawierać zestaw narzędzia wdrażania pakietu i folderu zasobów narzędzia wdrażania pakietu. Oznacza to, package.zip|
|SolutionAnchorName |Nazwa pliku zip rozwiązanie w narzędzia do wdrażania pakietów używany nazwę wyświetlaną i opis zasobów rozwiązania.|
| Oprócz parametru startDate| Jest to data pakietu rozwiązania staną się dostępne. Format jest MM/DD/RRRR|
|endDate|Jest to data pakietu rozwiązań zostanie zatrzymane, jest dostępna. Format jest MM/DD/RRRR |
|SupportedCountries |Jest to rozdzielana przecinkami lista krajów/regionów, które powinien zostać wyświetlony ten pakiet. Skontaktuj się z usług online, aby uzyskać listę wszystkich bieżącego numerów kierunkowych krajów. W czasie pisania tego dokumentu lista została: AE, AL, AM, AO, AR, AT, AUSTRALIA, AZ, BA, BB, BD, BYĆ, BG, BH, BM BN, BO, BR PRZEZ URZĄD CERTYFIKACJI, CH, CIĄGŁEJ INTEGRACJI, CL, CM, CO, CR, CV, EFEKTYWNA, CY, CZ, DE, DK, CZY, DZ, WE, ESTONIA, NA PRZYKŁAD, ES, FI, FR, GB, GE, GH, GR, GT, HK , HN, HR, HU, ID, IE, IL, INDIE, IQ, JEST, GO, JM, JO, JP, KE, KG, KN, KOREA, KW, KY, KZ, MODUŁU RÓWNOWAŻENIA OBCIĄŻENIA, LK, LT, LU, ŁOTWA, LY, MA, MC, MD, MNIE, MK, MN, MIESIĄC, MT, MU, MX, MY, NG, NI, NL, NO, NZ, OM, PA, PE, PH , KLUCZ PODSTAWOWY, PL, ŻĄDANIA ŚCIĄGNIĘCIA, PS, (CZAS PACYFICZNY), PY, ODPOWIEDZI NA PYTANIA, RO, R, RU, RW, SA, SE, SG, SI, SK, SN, SV, TH, TM, TN, TR, TT, TW, UA, STANY ZJEDNOCZONE, UY, UŻ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | Adres URL do strony więcej informacje dla tego pakietu. |
|Ustawienia regionalne|Jedno wystąpienie tego węzła dla każdego języka środowiska użytkownika, które mają być obsługiwane w rozwiązaniu preferowane najlepsze środowisko użytkownika. Ten węzeł zawiera elementy podrzędne, które opisują ustawień regionalnych, logo i warunki dla każdego z języków|
|Ustawienia regionalne: PackageLocale.Code|Identyfikator LCID języka dla tego węzła. Przykład: Angielski (US) to 1033|
|Ustawienia regionalne: PackageLocale.IsDefault|Wskazuje, że jest to domyślny język. Jest on używany jako spadek kopię języka, jeśli język interfejsu użytkownika, wybranej przez klienta jest niedostępny.|
|Ustawienia regionalne: Logo|To jeśli logo chcesz użyć dla tego pakietu. Rozmiar ikon to 32 x 32. Dozwolone formaty to PNG i JPG|
|Ustawienia regionalne: warunki: PackageTerm.File|Jest to nazwa pliku dokumentu HTML, zawierający postanowieniami licencyjnymi.|

Poniżej przedstawiono, w którym będą wyświetlane logo:

![CRMScreenShot5](media/CRMScreenShot5.png)

Ostatnim krokiem jest następujące informacje w jednym pliku zip.

1. ZIP (utworzone wcześniej)
2. **Content_Types.XML**
3. xml
4. PNG
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Zmień nazwę pliku na coś, co jest odpowiednia dla danej aplikacji. Firma Microsoft preferują obejmują z nazwy firmy i nazwy aplikacji. Na przykład: **_Microsoft_SamplePackage.zip**.
