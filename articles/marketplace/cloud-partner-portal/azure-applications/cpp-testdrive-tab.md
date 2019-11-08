---
title: Dysk testowy oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Jak skonfigurować dysk testowy dla oferty aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826118"
---
# <a name="azure-applications-test-drive-tab"></a>Karta dysk testowy aplikacji platformy Azure

Skorzystaj z karty test dysku, aby zapewnić klientom wersję próbną.

## <a name="test-drive-benefits"></a>Zalety testów

Tworzenie wersji próbnej dla klientów jest najlepszym rozwiązaniem w celu zapewnienia, że użytkownicy będą mogli kupować z pewnością. Dostępne opcje wersji próbnej są najbardziej efektywne w przypadku generowania potencjalnych klientów o wysokiej jakości i większej konwersji potencjalnych klientów.

Zapewnia ona klientom praktyczną, samodzielną wersję próbną najważniejszych funkcji i korzyści, które przedstawiono w rzeczywistym scenariuszu implementacji.

## <a name="how-a-test-drive-works"></a>Jak działa Stacja testowa

Potencjalni klienci przeszukują i odnajdują aplikację w portalu Marketplace. Klient loguje się i zgadza się na warunki użytkowania. W tym momencie klient otrzymuje wstępnie skonfigurowane środowisko w celu wypróbowania przez określoną liczbę godzin, a użytkownik otrzymuje wysoce wykwalifikowany klient. Aby uzyskać więcej informacji, zobacz [co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Konfigurowanie dysku testowego

Wykonaj następujące kroki, aby włączyć i skonfigurować dysk testowy.

### <a name="to-enable-a-test-drive"></a>Aby włączyć stację testową:

1. W obszarze **Nowa oferta**wybierz kartę **dysk testowy** .
2. W obszarze **Testuj dysk**wybierz opcję **tak** , aby **włączyć stację testową**.

   ![Włącz stację testową](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Aby skonfigurować stację testową:

Po włączeniu dysku testowego należy wypełnić następujące formularze, aby skonfigurować dysk testowy:
  
 - Szczegóły
 - Konfiguracja techniczna
 - Szczegóły subskrypcji wdrożenia dysku testowego

Przechwytywanie następnego ekranu pokazuje wszystkie formularze na dysku testowym. Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest to wymagane. 

![Konfigurowanie dysku testowego](./media/managed-app-configure-testdrive.png)

W poniższej tabeli opisano pola wymagane do skonfigurowania dysku testowego dla aplikacji zarządzanej.  Pola dołączone do gwiazdki są wymagane.

|      Pole         |  Opis      |
|  ---------------   |  ---------------  |
| **\* opisu**  |  Opisz, co można zrobić na dysku testowym. Możesz użyć podstawowych tagów HTML do formatowania tego opisu. Na przykład &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;olu&gt;i nagłówki.                |
| **Ręczne\* użytkownika**  |  Przekaż użytkownikowi Podręcznik użytkownika, którego mogą używać klienci, aby zapoznać się ze sposobem testowania środowiska testowego. Ten dokument musi być plikiem PDF.    |
| **Wideo demonstracyjne na dysku testowym** |  Opcjonalny Przewodnik wideo dotyczący Twojego dysku testowego. Klient może obejrzeć ten film wideo przed przełączeniem na dysk testowy. Podaj adres URL wideo w serwisie YouTube lub Vimeo. Jeśli wybierzesz opcję **+ Dodaj wideo**, zostanie wyświetlony monit o podanie następujących informacji:<ul><li>Nazwa</li><li>Adres URL</li><li>Miniatura (w formacie PNG, 533 x 324 pikseli)</li></ul>  |
| **\* wystąpień**      | Skonfiguruj liczbę potrzebnych wystąpień, w jakich regionach i jak szybko klienci mogą uzyskać dysk testowy. Aby uzyskać więcej informacji, zobacz [How to Publish a Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Czas trwania dysku testowego (w godzinach)\*** | Wprowadź liczbę całkowitą dla liczby godzin. Dozwolony zakres to od 1 do 999. |
| **\* szablonu ARM na dysku testowym**     | Przekaż plik skompresowany (. zip), który zawiera szablony Azure Resource Manager aplikacji. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **\* informacji o dostępie**          | Podaj informacje o dostępie, gdy klient pobierze dysk testowy. Na przykład adres URL służący do uzyskania dostępu do dysku testowego i informacje o podpisywaniu. . Możesz użyć podstawowych tagów HTML do formatowania tego opisu. Na przykład &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;olu&gt;i nagłówki. |
| **Identyfikator subskrypcji platformy Azure\***       | Pozwala to na dostęp do usług platformy Azure i Azure Portal. Subskrypcja polega na tym, że raportowane jest użycie zasobów, a usługi są rozliczane. Jeśli nie masz jeszcze oddzielnej subskrypcji platformy Azure dla dysków testowych, Utwórz subskrypcję.  |
| **Identyfikator dzierżawy usługi Azure AD\***          | Podaj istniejącą dzierżawę w Azure Active Directory lub Utwórz dzierżawcę dla tego dysku testowego.  |
| **Identyfikator aplikacja usługi Azure AD\***             | Utwórz i zarejestruj nową aplikację. Firma Microsoft używa tej aplikacji do wykonywania operacji w wystąpieniu dysku testowego.  |
| **\* klucza aplikacja usługi Azure AD**            | Utwórz klucz uwierzytelniania dla aplikacji i wklej go w tym polu.   |
|  |  |

Po podaniu wszystkich wymaganych informacji wybierz pozycję **Zapisz** , aby zakończyć konfigurowanie dysku testowego.


## <a name="next-steps"></a>Następne kroki

[Karta platformy handlowej](./cpp-marketplace-tab.md)
