---
title: Wiadomość e-mail z platformy Azure dotycząca zaległego salda
description: Opis sposobu dokonywania płatności w przypadku, gdy Twoja subskrypcja platformy Azure ma zaległe saldo
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: c042f5cee3d0a1e874729911a2162e48c1630a72
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226422"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure

Ten artykuł dotyczy klientów z kontami usługi online firmy Microsoft.

Jeśli nie otrzymamy Twojej płatności lub jeśli nie będziemy mogli jej przetworzyć, możesz otrzymać wiadomość e-mail lub zobaczyć alert w witrynie Azure Portal bądź w Centrum konta.
Jeśli jesteś [administratorem konta](billing-subscription-transfer.md#whoisaa), możesz rozliczyć zaległe opłaty w witrynie [Azure Portal](https://portal.azure.com). Jeśli Twoja płatność jest rozliczana na podstawie faktury, wyślij płatność do lokalizacji wskazanej na dole faktury.

> [!IMPORTANT]
> * Jeśli masz wiele subskrypcji korzystających z tej samej karty kredytowej i za wszystkie z nich masz zaległe płatności, musisz jednorazowo uregulować całe należne saldo.
> * Instrument płatniczy, za pomocą którego rozliczysz zaległe opłaty, stanie się nową aktywną formą płatności dla wszystkich subskrypcji, które korzystały z formy płatności zakończonej niepowodzeniem.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Rozwiązywanie problemów dotyczących zaległego salda w witrynie Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. Na stronie Przegląd zostanie wyświetlona lista subskrypcji. Jeśli stan subskrypcji wskazuje, że minął termin płatności, kliknij link **Ureguluj saldo**.
    ![Zrzut ekranu przedstawiający link do rozliczania salda](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Łączne należne saldo odzwierciedla zaległe opłaty za wszystkie usługi firmy Microsoft korzystające z formy płatności zakończonej niepowodzeniem.
1. Wybierz formę płatności, aby uregulować saldo. Ta forma płatności stanie się aktywną formą płatności dla wszystkich subskrypcji, które aktualnie korzystają z formy płatności zakończonej niepowodzeniem.
    ![Zrzut ekranu pokazujący link umożliwiający wybór formy płatności](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Jeśli wybrana forma płatności ma także zaległe opłaty za usługi firmy Microsoft, zostanie to odzwierciedlone w łącznym należnym saldzie. Te zaległe opłaty muszą zostać również uregulowane.
1. Kliknij pozycję **Zapłać**.

## <a name="troubleshoot-declined-credit-card"></a>Rozwiązywanie problemów z odrzuconą kartą kredytową

W razie odrzucenia przez instytucję finansową płatności za pomocą karty kredytowej skontaktuj się z tą instytucją, aby rozwiązać ten problem. W swoim banku upewnij się, że spełnione są te warunki:
- Na karcie są włączone transakcje międzynarodowe.
- Karta ma wystarczający limit kredytowy lub środki do rozliczenia salda.
- Na karcie są włączone płatności cykliczne.

## <a name="not-getting-billing-email-notifications"></a>Nie otrzymujesz powiadomień e-mail dotyczących rozliczeń?

Jeśli jesteś administratorem konta, [sprawdź, jaki adres e-mail jest używany na potrzeby powiadomień](billing-how-to-change-azure-account-profile.md). Zalecamy korzystanie z adresu e-mail, który jest regularnie sprawdzany. Jeśli adres e-mail jest prawidłowy, sprawdź swój folder spamu.

## <a name="if-i-forget-to-pay-what-happens"></a>Co się stanie, jeśli zapomnę uregulować płatność?

Usługa zostanie anulowana, a Twoje zasoby nie będą już dostępne. Twoje dane zostaną usunięte z platformy Azure po 90 dniach od zakończenia usługi. Aby dowiedzieć się więcej, zobacz [Centrum zaufania firmy Microsoft — jak zarządzamy Twoimi danymi](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Jeśli wiesz, że Twoja płatność została przetworzona, ale Twoja subskrypcja jest nadal wyłączona, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
