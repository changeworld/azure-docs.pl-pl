---
title: Wersji testowej oferty w usłudze Azure Application | Dokumentacja firmy Microsoft
description: Jak skonfigurować wersję testową aplikacji na platformie Azure oferty w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: d51c7330acd37d8a861c136ea85a2ff456eb264e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744731"
---
# <a name="azure-applications-test-drive-tab"></a>Karta wersję testową aplikacji platformy Azure

Karta wersji testowej zapewniając wersji próbnej środowisko dla swoich klientów.

## <a name="test-drive-benefits"></a>Testowanie korzyści ze stosowania dysków

Tworzenie wersji próbnej środowisko dla swoich klientów jest najlepszym rozwiązaniem, aby upewnić się, że mogą kupić bez obaw. Z dostępnych opcji wersji próbnej wersji testowej jest najbardziej efektywne, wysokiej jakości generowania potencjalnych klientów i zwiększonej konwersji tych potencjalnych klientów.

Daje klientom z wersją próbną praktyczne, przeprowadzanemu samodzielnie procesowi kluczowe funkcje i korzyści, przedstawione w scenariuszu wdrożenia rzeczywistych swojego produktu.

## <a name="how-a-test-drive-works"></a>Jak działa wersji testowej

Potencjalny klient wyszukuje i umożliwia odnalezienie aplikacji w witrynie Marketplace. Klient rejestruje i wyraża zgodę na warunki użytkowania. W tym momencie użytkownik otrzymał wstępnie skonfigurowanego środowiska do wypróbowania funkcji przez określoną liczbę godzin, gdy otrzymasz wysoko wykwalifikowanych potencjalnego klienta do monitowanie. Aby uzyskać więcej informacji, zobacz [co to jest wersja testowa?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Konfigurowanie wersji testowej

Wykonaj następujące kroki, aby włączyć i skonfigurować wersję testową.

### <a name="to-enable-a-test-drive"></a>Aby włączyć wersji testowej:

1. W obszarze **nowa oferta**, wybierz opcję **wersji testowej** kartę.
2. W obszarze **wersji testowej**, wybierz opcję **tak** dla **włączyć wersję testową**.

   ![Włączanie wersji testowej](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Aby skonfigurować wersji testowej:

Po włączeniu wersji testowej, będzie wypełnij następujące formy konfigurowania wersji testowej:
  
 - Szczegóły
 - Pomoc konfiguracji
 - Szczegóły subskrypcji wdrożenia dysku testu

Następny zrzut ekranu pokazuje wszystkie formularze wersji testowej. Znak gwiazdki (*) dodanym na końcu nazwy pola wskazuje, że jest to wymagane. 

![Konfigurowanie wersji testowej](./media/managed-app-configure-testdrive.png)

W poniższej tabeli opisano pola wymagane do skonfigurowania wersję testową dla aplikacji zarządzanej.

|    **Pole**       |  **Opis**  |
|  ---------------   |  ---------------  |
|      Opis              |   Opisz, co można zrobić w wersji testowej. Aby sformatować ten opis, można użyć podstawowa tagów HTML. Na przykład &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;i nagłówki.                |
|    Podręcznik użytkownika                |     Przekaż podręcznika użytkownika używanego przez klientów do zaprezentowania środowisko wersji testowej. Ten dokument musi być plikiem PDF.              |
|         Test Drive demonstracyjny materiał wideo (opcjonalnie)           |       Możesz podać przewodnik wideo dotyczący wersji testowej. Klient może Obejrzyj ten film, zanim one wypróbować wersję testową. Podaj adres URL pliku wideo w usłudze YouTube lub Vimeo. Jeśli wybierzesz **+ Dodaj wideo**, zostanie wyświetlony monit o podanie następujących informacji:<ul><li>Name (Nazwa)</li><li>Adres URL</li><li>Miniatura (w formacie PNG, 533 x 324 pikseli)</li></ul>            |
|       Wystąpienia             |        Ile wystąpień, które chcesz skonfigurować, w jakie regiony i jak szybko klienci mogą uzyskać wersję testową. Aby uzyskać więcej informacji, zobacz [jak opublikować wersję testową](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
|       Dysk czas trwania testu (godziny)             |       Wprowadź liczbę całkowitą liczbę godzin. Dozwolony zakres to od 1 do 999.            |
|        Testuj szablon ARM dysku            |        Przekaż plik skompresowany (.zip) z szablonów usługi Azure Resource Manager dla aplikacji. Aby uzyskać więcej informacji, zobacz [usługi Azure Resource Manager testowej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).            |
|        Uzyskiwanie dostępu do informacji            |         Zapewnia dostęp do informacji po klient pobiera wersję testową. Na przykład adres URL, dostęp do wersji testowej, a zarejestrować informacje. . Aby sformatować ten opis, można użyć podstawowa tagów HTML. Na przykład &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;i nagłówki.          |
|       Identyfikator subskrypcji dostępu             |       Spowoduje to przydzielenie dostępu do usług platformy Azure i witryny Azure portal. Subskrypcja jest, gdzie raportowania użycia zasobów i usługi są rozliczane. Jeśli nie masz jeszcze oddzielną subskrypcję platformy Azure dla wersji testowych tylko, należy utworzyć subskrypcję.             |
|          Identyfikator dzierżawy usługi Azure AD          |        Podaj istniejącą dzierżawę usługi Azure Active Directory, albo utworzyć dzierżawę dla tej wersji testowej.           |
|         Identyfikator aplikacji w usłudze Azure AD           |       Utwórz i zarejestruj nową aplikację. Firma Microsoft korzysta z tej aplikacji do wykonywania operacji w ramach wystąpienia wersji testowej.            |
|          Klucz aplikacji w usłudze Azure AD          |         Utwórz klucz uwierzytelniania dla aplikacji i wklej go w tym polu.          |

Po podaniu wymaganych informacji wybierz **Zapisz** aby zakończyć konfigurowanie wersji testowej.

## <a name="next-steps"></a>Kolejne kroki

[Karta platformy handlowej](./cpp-marketplace-tab.md)