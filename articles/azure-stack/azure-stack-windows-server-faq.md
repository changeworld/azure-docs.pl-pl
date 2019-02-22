---
title: Serwer systemu Windows w usłudze Azure Stack powiązane — często zadawane pytania | Dokumentacja firmy Microsoft
description: Lista często zadawanych pytań w portalu Marketplace usługi Azure Stack dla systemu Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: b498283ee117935438c55e5162e92acdb0f5ebfe
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649904"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>System Windows Server w usłudze Azure Stack Portal Marketplace — często zadawane pytania

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące obrazów systemu Windows Server w [usługi Azure Stack w portalu Marketplace](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Elementy portalu Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Jak zaktualizować do nowszej obrazu systemu Windows

Po pierwsze należy ustalić, jeśli wszystkie szablony usługi Azure Resource Manager, zapoznaj się do konkretnych wersji. Jeśli tak, zaktualizuj te szablony, lub zachować starszych wersji obrazu. Zaleca się używać **wersji: najnowsze**.

Następnie, jeśli wszystkie zestawy skalowania maszyn wirtualnych odwołują się do określonej wersji, możesz pomyśleć o czy je później skalowania i zdecyduj, czy chcesz zachować starsze wersje. Jeśli żadna z tych warunków zastosowania, należy usunąć starsze obrazy w portalu Marketplace, przed pobraniem tych nowszej. Aby to zrobić, jeśli jest to, jak oryginalny został pobrany, należy użyć zarządzania portalu Marketplace. Następnie Pobierz nowszą wersję.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Jakie są opcje licencjonowania dla systemu Windows Server w portalu Marketplace, obrazy w usłudze Azure Stack?

Firma Microsoft oferuje dwie wersje obrazów systemu Windows Server w portalu Azure Marketplace stosu:

- **Płatność proporcjonalna używasz**: Te obrazy Uruchom liczniki Windows pełną cenę.
   Kto powinien używać: Klienci z umową Enterprise Agreement (EA), którzy korzystają *model rozliczeń za użycie*; Dostawcy usług kryptograficznych, którzy nie chcą korzystanie z licencjonowania w ramach umowy SPLA.
- **Model dostarczania własnej licencji (BYOL)**: Te obrazy Uruchom liczniki podstawowe.
   Kto powinien używać: Umową EA z licencji systemu Windows Server; Dostawcy usług kryptograficznych, którzy korzystają z licencji w ramach umowy SPLA.

Azure hybrydowego użycia korzyści (AHUB) nie jest obsługiwane w usłudze Azure Stack. Klienci, którzy licencji za pośrednictwem modelu "Pojemność" należy użyć obrazu BYOL. W przypadku testowania przy użyciu usługi Azure Stack Development Kit (ASDK), można użyć jednej z tych opcji.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Co zrobić, jeśli pobrano niewłaściwej wersji do zaoferowania dzierżawcom/użytkowników?

Najpierw usuń wersję nieodpowiednią za pośrednictwem witryny Marketplace zarządzania. Poczekaj na jego zakończenie pełni (Spójrz powiadomienia o zakończeniu nie blok zarządzania portalu Marketplace). Następnie Pobierz poprawnej wersji.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Co zrobić, jeśli Moja użytkownika niepoprawnie zaznaczono pole "Mam licencję" w poprzednich Windows tworzy, a nie posiadają licencji?

Zobacz [konwersji maszyn wirtualnych dzięki korzyściom z powrotem do płatności](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Co zrobić, jeśli masz starszą obrazu i Moje użytkownik zapomniał Sprawdź pole "Mam licencję" lub firma Microsoft korzystanie z własnych obrazów i mamy uprawnienie umowy Enterprise Agreement?

Zobacz [przekonwertować istniejącą Maszynę wirtualną przy użyciu usługi Azure korzyści użycia hybrydowego dla systemu Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Należy pamiętać, że korzyść użycia hybrydowego platformy Azure nie ma zastosowania do usługi Azure Stack, ale Zastosuj efekt tego ustawienia.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Co pozostałe maszyny wirtualne, które używają systemu Windows Server, takich jak bazy danych SQL lub serwer Machine Learning?

Tych obrazów należy stosować **licenseType** parametru, dzięki czemu są one płatności, jak używasz. Ten parametr zostanie ustawiony (odpowiedź Zobacz poprzednie często zadawane pytania). Dotyczy to tylko systemu Windows Server oprogramowania nie warstwowej produktów, takich jak SQL, które wymagają model dostarczania własnej licencji. Płatność zgodnie z korzystanie z licencjonowania nie ma zastosowania do oprogramowania warstwowej.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Mam umowy Enterprise Agreement (EA) i będzie korzystać z moją licencję EA systemu Windows Server; jak upewnić się, że obrazy są naliczane poprawnie?

Możesz dodać **licenseType: Windows_Server** w szablonie usługi Azure Resource Manager. To ustawienie, należy dodać do każdego bloku zasobu maszyny wirtualnej.

## <a name="activation"></a>Uaktywnienie

Aby aktywować maszyny wirtualnej systemu Windows Server w usłudze Azure Stack, muszą być spełnione następujące warunki:

- Producent OEM został ustawiony odpowiednie znacznika systemu BIOS w każdym systemie hosta w usłudze Azure Stack.
- Windows Server 2012 R2 i Windows Server 2016, należy użyć [automatyczna aktywacja maszyny wirtualnej](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Usługa zarządzania kluczami (KMS) i innych usług aktywacji nie są obsługiwane w usłudze Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Jak zweryfikować, że Moja maszyna wirtualna została aktywowana?

Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

```shell
slmgr /dlv
```

Jeśli poprawnie włączono, zobaczysz następujący wyraźnie wskazane i nazwę hosta wyświetlane w `slmgr` danych wyjściowych. Nie są zależne od znaków wodnych na ekranie, jak mogą być nieaktualne lub są wyświetlane na innej maszynie wirtualnej za Ciebie.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Nie ustawiono mojej maszyny Wirtualnej do użycia funkcja AVMA, jak to naprawić?

Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

```shell
slmgr /ipk <AVMA key>
```

Zobacz [automatyczna aktywacja maszyny wirtualnej](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) artykułu kluczy do użycia dla obrazu.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Czy mogę tworzyć własne obrazy systemu Windows Server, jak można upewnić się, że ich za pomocą funkcji AVMA?

Zalecane jest, że wykonywanie `slmgr /ipk` wiersza polecenia przy użyciu odpowiedniego klucza przed uruchomieniem `sysprep` polecenia. Lub, Uwzględnij klucz AVMA w dowolnym pliku instalacji Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Próbuję Użyj Mój obraz systemu Windows Server 2016, utworzony na platformie Azure i jest Aktywacja lub nie przy użyciu aktywacji usługi KMS.

Uruchom polecenie `slmgr /ipk`. Obrazy platformy Azure może prawidłowo wrócił do AVMA, ale jeśli dotrą do systemu Azure usługi KMS, będą aktywowane. Upewnij się, że te maszyny wirtualne są ustawione za pomocą funkcji AVMA jest zalecane.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Czy wykonano wszystkie kroki, ale Moje maszyny wirtualne są nadal powstrzymanie się.

Skontaktuj się z dostawcą sprzętu, aby sprawdzić, czy zostały zainstalowane poprawne znaczniki systemu BIOS.

### <a name="what-about-earlier-versions-of-windows-server"></a>Co z wcześniejszych wersji systemu Windows Server?

[Automatyczna aktywacja maszyny wirtualnej](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) nie jest obsługiwane we wcześniejszych wersjach systemu Windows Server. Trzeba będzie ręcznie uaktywnić maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Omówienie usługi Azure Stack w portalu Marketplace](azure-stack-marketplace.md)
- [Pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack](azure-stack-download-azure-marketplace-item.md)
