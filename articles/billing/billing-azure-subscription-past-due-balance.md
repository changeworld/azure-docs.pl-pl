---
title: Minął termin płatności saldo e-mail z platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób dokonać płatności jeśli Twoja subskrypcja platformy Azure ma przeszłych płatności salda
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: 45b5d2c94a72cb23ef2b4a8db100623b0c58b0a9
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66023400"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Rozwiąż zaległe saldo dla Twojej subskrypcji platformy Azure

Jeśli nie otrzyma płatności lub nie można przetworzyć płatności, może otrzymasz wiadomość e-mail lub wyświetlony alert w witrynie Azure portal lub w Centrum konta.
Jeśli jesteś [administratora konta](billing-subscription-transfer.md#whoisaa), można uregulować zaległe opłaty [witryny Azure portal](https://portal.azure.com). Jeśli użytkownik pracuje na invoice mode of payment płatności, Wyślij płatność do lokalizacji wskazanej na dole faktury.

> [!IMPORTANT]
> * Jeśli masz wiele subskrypcji przy użyciu tej samej karty kredytowej, są one wszystkie zaległe całego zaległe saldo musisz zapłacić tylko raz.
> * Instrument płatniczy, którego używasz do Ureguluj zaległe opłaty staną się nowych aktywną formę płatności dla wszystkich subskrypcji, w których zastosowano metodę płatności nie powiodło się.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Rozwiąż zaległe saldo w witrynie Azure portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator konta.
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. Na stronie Przegląd zobaczysz listę subskrypcji. Stan swojej subskrypcji jest zaległa, kliknij przycisk **rozliczenia Saldo** łącza.
    ![Zrzut ekranu pokazujący rozliczenia saldo łącza](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Łączna liczba zaległe saldo odzwierciedla zaległe opłaty dla wszystkich usług firmy Microsoft przy użyciu metody płatności nie powiodło się.
1. Wybierz metodę płatności, aby płacą. Ta sama forma płatności staną się aktywną formę płatności dla wszystkich subskrypcji, które obecnie przy użyciu metody płatności nie powiodło się.
    ![Zrzut ekranu przedstawiający link metody płatności wybierz](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Jeśli wybrana metoda płatności ma również zaległe opłaty dla usług firmy Microsoft, to zostaną odzwierciedlone w całkowitej zaległe saldo. Te opłaty zaległe musisz zapłacić za.
1. Kliknij przycisk **płacić**.

## <a name="not-getting-billing-email-notifications"></a>Nie otrzymuje powiadomienia e-mail rozliczeń?

Jeśli jesteś administratorem konta [sprawdzić, jakiego adresu e-mail jest używany dla powiadomień](billing-how-to-change-azure-account-profile.md). Zaleca się, że używasz adresu e-mail, które regularnie sprawdzać. Jeśli adres e-mail jest odpowiednia, sprawdź folder wiadomości-śmieci.

## <a name="if-i-forget-to-pay-what-happens"></a>Jeśli nie pamiętam płatności, co się dzieje?

Usługa została anulowana i zasobami nie będą już dostępne. Twoje dane platformy Azure jest usunięte 90 dni po usługi zostanie zakończony. Aby dowiedzieć się więcej, zobacz [Microsoft Trust Center — jak Zarządzamy danych](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Jeśli znasz przetworzeniu płatności, ale Twoja subskrypcja nadal jest wyłączona, skontaktuj się z [pomocy technicznej systemu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
