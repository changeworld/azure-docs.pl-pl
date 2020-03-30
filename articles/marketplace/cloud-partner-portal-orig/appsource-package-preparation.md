---
title: Przygotowanie pakietu AppSource | Azure Marketplace
description: Explanaion w jaki sposób przygotować i zbudować pakiety AppSource .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280613"
---
# <a name="appsource-package-preparation"></a>Przygotowanie pakietu AppSource

Oprócz pliku solution.zip potrzebny jest pakiet **AppSource**. Jest to plik .zip, który zawiera wszystkie zasoby potrzebne do automatyzacji procesu wdrażania rozwiązania w środowisku CRM klientów. **Pakiet AppSource** zawiera następujące składniki

* Pakiet dla wdrażacza pakietów
* **Content_Types.xml** z używanymi zasobami
* xml z danymi specyficznymi dla aplikacji
* Logo 32x32, które będzie wyświetlane wraz z twoją wizytówką w Centrum administracyjnym
* Postanowienia licencyjne, polityka prywatności

Poniższe kroki pomogą Ci utworzyć pakiet AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Tworzenie pakietu dla wdrażacza pakietów

Pakiet dla wdrażacza pakietów jest jedną z części pakietu AppSource.

Aby utworzyć pakiet dla wdrażacza pakietu, należy [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)użyć następujących instrukcji: . Po zakończeniu, pakiet będzie składać się z aktywów poniżej:

1. Folder pakietów: zawiera wszystkie rozwiązania, dane konfiguracyjne, pliki płaskie i zawartość pakietu. _Uwaga: W poniższym przykładzie założymy, że folder pakietu nosi nazwę "PkgFolder"_
2. dll: Zestaw zawiera niestandardowy kod dla pakietu. _Uwaga: W poniższym przykładzie założymy, że ten plik nosi nazwę "MicrosoftSample.dll"._

Teraz musisz utworzyć plik o nazwie "**Content_Types.xml**" Ten plik wyświetli listę wszystkich rozszerzeń zasobów, które są częścią pakietu. Oto przykładowy kod pliku.

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

Ostatnim krokiem jest skompresować następujące pliki w jednym pliku. Nazwij to **package.zip**. Będzie ona zawierać

1. PkgFolder (w tym wszystko wewnątrz folderu)
2. Dll
3. **Content_Types.xml**

Kroki tworzenia pliku package.zip:

1. Umieść folder pakietu, **plik Content_Types.xml** i PackageName.dll w jednym katalogu.

![Zdjęcie CRMScreenShot2](media/CRMScreenShot2.png)

1. Zaznacz wszystkie elementy w folderze, kliknij prawym przyciskiem myszy i wybierz polecenie Wyślij do skompresowanego (zip) folder

![Zdjęcie CRMScreenShot3](media/CRMScreenShot3.png)

1. Zmienianie nazwy na package.zip

![Zdjęcie CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Tworzenie pakietu appsource

Pakiet AppSource wymaga kilku dodatkowych plików.

1. jpg (rozdzielczość 32x32)
2. html (plik w formacie HTML)
3. **Content_Types.xml** (tak samo jak powyżej)
4. xml

Oto przykładowy kod pliku input.xml. Zobacz definicje w poniższej tabeli.

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
|ProviderName|Z kim pochodzi rozwiązanie. Jeśli zespół firmy Microsoft, to powinien być Microsoft.|
|Plik pakietu |Zasoby programu wdrażania pakietu spakowane\_razem z plikiem content types.xml. Ten plik zip powinien zawierać zestaw wdrażający pakiet i folder z zasobami wdrażający pakiet. Oznacza to, że package.zip|
|Nazwa rozwiązaniaAnchorname |Nazwa pliku zip rozwiązania w rozmieszczacza pakietu, który jest używany dla nazwy wyświetlanej i opisu zasobów rozwiązania.|
| StartDate| Jest to data, że pakiet rozwiązania staną się dostępne. Format to MM/DD/YYYY|
|Enddate|Jest to data, że pakiet rozwiązania przestanie być dostępny. Format to MM/DD/YYYY |
|Obsługiwanekraje |Jest to lista krajów/regionów rozdzielona przecinkami, które powinny widzieć ten pakiet. Skontaktuj się z usługami online, aby uzyskać listę wszystkich aktualnych kodów krajów. W tym czasie, z tego pisania lista była: AE, AL, AM, AO, AR, AT, AU, AZ, BA, BB, BD, BE, BG, BH, BM, BN, BO, BR, BY, CA, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE, DK, DO, DZ, EC, EE, EG ,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,MY ,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZAN,ZAW |
|LearnMoreLink (Więcej niż w tym) | Adres URL strony z informacjami o tym pakiecie. |
|Ustawień regionalnych|Jedno wystąpienie tego węzła dla każdego języka ux, który chcesz obsługiwać w ux preferowanego rozwiązania. Ten węzeł zawiera elementy podrzędne opisujące ustawienia regionalne, logo i terminy dla każdego języka|
|Ustawienia regionalne: PackageLocale.Code|LCID języka dla tego węzła. Przykład: angielski amerykański to 1033|
|Ustawienia regionalne: PackageLocale.IsDefault|Wskazuje, że jest to język domyślny. Jest to używany jako język powrotu, jeśli język UX wybrany przez klienta nie jest dostępny.|
|Ustawienia regionalne: Logo|To, jeśli logo, którego chcesz użyć dla tego pakietu. Rozmiar ikony to 32x32. Dozwolone formaty to PNG i JPG|
|Ustawienia regionalne:Warunki: PackageTerm.File|Jest to nazwa pliku doc HTML, który zawiera postanowienia licencyjne.|

Oto, gdzie pojawi się logo:

![Zdjęcie CRMScreenShot5](media/CRMScreenShot5.png)

Ostatnim krokiem jest skompresować następujące pliki w jednym pliku.

1. zip (utworzony wcześniej)
2. **Content_Types.xml**
3. xml
4. png
5. html

![Zdjęcie CRMScreenShot6](media/CRMScreenShot6.png)

Zmień nazwę pliku na coś odpowiedniego dla aplikacji. Wolimy podać nazwę firmy i nazwę aplikacji. Na przykład: **_Microsoft_SamplePackage.zip**.
