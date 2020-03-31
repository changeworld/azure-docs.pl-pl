---
title: Skonfiguruj laboratorium koncentrujące się na uczeniu głębokim przy użyciu usług Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium do nauczania skryptów powłoki na Linuksie.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109396"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Skonfiguruj laboratorium koncentrujące się na głębokim uczeniu w przetwarzaniu języka naturalnego przy użyciu usług Azure Lab Services
W tym artykule pokazano, jak skonfigurować laboratorium koncentrujące się na uczeniu głębokim w przetwarzaniu języka naturalnego (NLP) przy użyciu usługi Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej inteligencji (AI), która umożliwia komputerom z możliwością tłumaczenia, rozpoznawania mowy i innych funkcji rozumienia języka.  

Uczniowie biorący klasę NLP otrzymują maszynę wirtualną systemu Linux (VM), aby dowiedzieć się, jak zastosować algorytmy sieci neuronowej do opracowywania modeli uczenia głębokiego, które są używane do analizowania napisanego języka ludzkiego. 

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services lub użyć istniejącego konta laboratorium. Zobacz następujący samouczek tworzenia nowego konta laboratorium: [Samouczek do konfigurowania konta laboratorium](tutorial-setup-lab-account.md).
 
Po utworzeniu konta laboratorium włącz następujące ustawienia na koncie laboratorium: 

| Ustawienie konta laboratorium | Instrukcje |
| ----------- | ------------ |  
| Obrazy z Marketplace | Włącz obraz maszyny wirtualnej do nauki o danych dla systemu Linux (Ubuntu) do użycia na koncie laboratorium.  Zapoznaj się z poniższymi artykułami, aby uzyskać instrukcje: [Określanie obrazów portalu Marketplace dostępnych dla twórców laboratoriów.](specify-marketplace-images.md) | 

Wykonaj [ten samouczek,](tutorial-setup-classroom-lab.md) aby utworzyć nowe laboratorium i zastosować następujące ustawienia:

| Ustawienia laboratorium | Wartość/instrukcja | 
| ------------ | ------------------ |
| Rozmiar maszyny wirtualnej | Mały procesor graficzny (Compute). Ten rozmiar najlepiej nadaje się do aplikacji intensywnie korzystających z zasobów obliczeniowych i sieciowych, takich jak sztuczna inteligencja i uczenie głębokie. |
| Obraz maszyny Wirtualnej | [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Ten obraz zawiera struktury uczenia głębokiego i narzędzia do uczenia maszynowego i nauki o danych. Aby wyświetlić pełną listę zainstalowanych narzędzi na tym obrazie, zobacz następujący artykuł: [Co znajduje się w systemie DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Włączanie połączenia pulpitu zdalnego | Włącz. <p>Włączenie tego ustawienia umożliwi nauczycielom i uczniom łączenie się z maszynami wirtualnymi za pomocą pulpitu zdalnego (RDP).</p><p>**Ważne:** RDP jest już zainstalowany i skonfigurowany na maszynie wirtualnej do nauki o danych dla obrazu Linuksa. W rezultacie nauczyciele/uczniowie mogą łączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP bez żadnych dodatkowych kroków. Ponadto, jeśli chcesz połączyć się z pulpitem graficznym, ten obraz ma już zainstalowany [serwer X2Go](https://wiki.x2go.org/doku.php/doc:newtox2go) server na maszynie wirtualnej. Studenci muszą zainstalować klienta X2Go na swoich komputerach lokalnych i musi używać klienta do nawiązania połączenia. Aby uzyskać więcej informacji, zobacz następujące przewodniki: <ul><li>[Jak uzyskać dostęp do maszyny wirtualnej do nauki o danych dla systemu Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Łączenie się z szablonem maszyny Wirtualnej w celu zainstalowania pakietów RDP i GUI](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Maszyna wirtualna do nauki o danych dla systemu Linux obraz zawiera niezbędne struktury uczenia głębokiego i narzędzia wymagane dla tego typu klasy. W rezultacie po utworzeniu maszyny szablonu nie trzeba go dalej dostosowywać. Może być opublikowany dla studentów do wykorzystania. Wybierz przycisk **Publikuj** na stronie szablonu, aby opublikować szablon w laboratorium.  

## <a name="cost"></a>Koszty
Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć następującego przykładu: 

Dla klasy 25 uczniów z 20 godzinami zaplanowanego czasu zajęć i 10 godzinami przydziału na pracę domową lub zadania, cena za laboratorium będzie - 25 studentów * (20 + 10) godzin * 139 jednostek laboratoryjnych * 0.01 USD za godzinę = 1042.5 USD

Więcej informacji na temat cen można znaleźć w [cenniku usług Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie
W tym artykule przeszedł przez kroki, aby utworzyć laboratorium dla klasy przetwarzania języka naturalnego. Można użyć podobnej konfiguracji dla innych klas uczenia głębokiego.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne dla konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail linki do rejestracji do studentów](how-to-configure-student-usage.md#send-invitations-to-users). 

