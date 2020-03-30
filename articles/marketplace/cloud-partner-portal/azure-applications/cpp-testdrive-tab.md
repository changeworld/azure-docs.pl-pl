---
title: Usługa Azure Application offer test drive | Azure Marketplace
description: Jak skonfigurować ofertę jazdy testowej dla aplikacji platformy Azure w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289009"
---
# <a name="azure-applications-test-drive-tab"></a>Karta Dysk testowy aplikacji platformy Azure

Użyj karty Test Drive, aby zapewnić klientom wersję próbną.

## <a name="test-drive-benefits"></a>Korzyści z jazdy próbne

Tworzenie środowiska próbnego dla klientów jest najlepszą praktyką, aby upewnić się, że mogą kupować z ufnością. Z dostępnych opcji próbnych, Test Drive jest najbardziej skuteczny w generowaniu wysokiej jakości potencjalnych klientów i zwiększonej konwersji tych potencjalnych klientów.

Zapewnia klientom praktyczną, samodzielną wersję próbną kluczowych funkcji i korzyści produktu, przedstawioną w rzeczywistym scenariuszu wdrażania.

## <a name="how-a-test-drive-works"></a>Jak działa dysk testowy

Potencjalny klient wyszukuje i odnajduje aplikację w portalu Marketplace. Klient loguje się i zgadza się na warunki użytkowania. W tym momencie klient otrzymuje wstępnie skonfigurowane środowisko, aby spróbować przez określoną liczbę godzin, podczas gdy otrzymasz wysoko wykwalifikowany lead do podjęcia działań następczych. Aby uzyskać więcej informacji, zobacz [Co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Konfigurowanie jazdy próbne

Aby włączyć i skonfigurować jazdę próbną, należy wykonać następujące czynności.

### <a name="to-enable-a-test-drive"></a>Aby włączyć jazdę próbną:

1. W obszarze **Nowa oferta**wybierz kartę **Jazdę próbna.**
2. W obszarze **Test Drive**wybierz pozycję **Tak,** aby **włączyć jazdę próbną**.

   ![Włączanie dysku testowego](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Aby skonfigurować jazdę próbną:

Po włączeniu jazdy próbowej, aby skonfigurować jazdę próbną, należy wypełnić następujące formularze:
  
 - Szczegóły
 - Konfiguracja techniczna
 - Szczegóły subskrypcji wdrożenia dysku testowego

Następne przechwytywanie ekranu pokazuje wszystkie formularze dysku testowego. Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest ona wymagana. 

![Konfigurowanie dysku testowego](./media/managed-app-configure-testdrive.png)

W poniższej tabeli opisano pola wymagane do skonfigurowania dysku testowego dla aplikacji zarządzanej.  Pola dołączone do gwiazdki są wymagane.

|      Pole         |  Opis      |
|  ---------------   |  ---------------  |
| **Opis\***  |  Opisz, co można zrobić na dysku testowym. Do sformaowania tego opisu można użyć podstawowych tagów HTML. Na przykład &lt;&gt;p &lt;,&gt; &lt;em , &lt;&gt;ul&gt;, &lt;li&gt;, ol i nagłówki.                |
| **Instrukcja obsługi\***  |  Przekaż instrukcję obsługi, której klienci mogą użyć, aby przejść przez środowisko jazdy testowej. Ten dokument musi być plikiem pdf.    |
| **Film demonstracyjny dysku testowego** |  Opcjonalny przewodnik wideo z jazdy testowej. Klient może obejrzeć ten film przed rozpoczęciem jazdy próbowej. Podaj adres URL filmu w YouTube lub Vimeo. Jeśli wybierzesz **+ Dodaj wideo,** zostanie wyświetlony monit o podanie następujących informacji:<ul><li>Nazwa</li><li>Adres URL</li><li>Miniatura (w formacie PNG, 533 x 324 piksele)</li></ul>  |
| **Wystąpień\***      | Skonfiguruj, ile wystąpień chcesz, w jakich regionach i jak szybko klienci mogą uzyskać jazdę testowa. Aby uzyskać więcej informacji, zobacz [Jak opublikować jazdę próbną](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Czas trwania dysku próbkowego (w godzinach)\*** | Wprowadź liczbę całkowitą dla liczby godzin. Dozwolony zakres wynosi od 1 do 999. |
| **Szablon ARM dysku testowego\***     | Przekaż skompresowany plik (.zip), który zawiera szablony usługi Azure Resource Manager dla aplikacji. Aby uzyskać więcej informacji, zobacz [Dysk testowy usługi Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Informacje o dostępie\***          | Podasz informacje o dostępie po tym, jak klient otrzyma testowy napęd. Na przykład adres URL, aby uzyskać dostęp do dysku testowego i podpisać informacje. . Do sformaowania tego opisu można użyć podstawowych tagów HTML. Na przykład &lt;&gt;p &lt;,&gt; &lt;em , &lt;&gt;ul&gt;, &lt;li&gt;, ol i nagłówki. |
| **Identyfikator subskrypcji platformy Azure\***       | Zapewnia to dostęp do usług platformy Azure i witryny Azure portal. Subskrypcja jest, gdzie użycie zasobów jest zgłaszane, a usługi są rozliczane. Jeśli nie masz jeszcze tylko oddzielnej subskrypcji platformy Azure tylko dla dysków testowych, utwórz subskrypcję.  |
| **Identyfikator dzierżawy usługi Azure AD\***          | Podaj istniejącą dzierżawę w usłudze Azure Active Directory lub utwórz dzierżawę dla tego dysku testowego.  |
| **Identyfikator aplikacji usługi Azure AD\***             | Tworzenie i rejestrowanie nowej aplikacji. Firma Microsoft używa tej aplikacji do wykonywania operacji w wystąpieniu dysku testowego.  |
| **Klucz aplikacji usługi Azure AD\***            | Utwórz klucz uwierzytelniania dla aplikacji i wklej go do tego pola.   |
|  |  |

Po podasz wszystkie wymagane informacje wybierz **pozycję Zapisz,** aby zakończyć konfigurowanie jazdy testowej.


## <a name="next-steps"></a>Następne kroki

[Karta platformy handlowej](./cpp-marketplace-tab.md)
