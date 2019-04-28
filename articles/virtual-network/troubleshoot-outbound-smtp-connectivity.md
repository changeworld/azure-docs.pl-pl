---
title: Rozwiązywanie problemów z połączenia wychodzącego SMTP na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy dotyczące łączności wychodzącej SMTP na platformie Azure.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 385163d791bff0c02a05ee1b27afd82c3afd0ac3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401923"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Wychodzący SMTP Rozwiązywanie problemów z łącznością na platformie Azure

Począwszy od 15 listopada 2017 r. wiadomości wychodzących wiadomości e-mail, które są wysyłane bezpośrednio do domen zewnętrznych (takich jak outlook.com i gmail.com) z maszyny wirtualnej (VM) są udostępniane tylko do niektórych typów subskrypcji w systemie Microsoft Azure. Połączenia wychodzące SMTP, które używają portu 25 protokołu TCP zostały zablokowane. (Port 25 jest używany głównie do dostarczenia nieuwierzytelnionych wiadomości e-mail.)

Ta zmiana w zachowaniu dotyczy tylko nowe subskrypcje i nowych wdrożeń od 15 listopada 2017 r.

## <a name="recommended-method-of-sending-email"></a>Zalecaną metodą wysyłania wiadomości e-mail
Zaleca się, że używasz usług uwierzytelnionego przekazywania SMTP, (które zwykle nawiązać połączenie za pośrednictwem portu TCP 587 i 443, ale zbyt obsługi innych portów) do wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure lub usługi Azure App Services. Te usługi są używane do obsługi reputacji adresu IP lub domeny, aby zminimalizować prawdopodobieństwo, że dostawcy poczty e-mail innych firm spowoduje odrzucenie wiadomości. Takie usługi przekazywania SMTP obejmują, ale nie są ograniczone do [SendGrid](https://sendgrid.com/partners/azure/). Użytkownik może również mieć bezpieczny usługi przekazywania SMTP, który jest uruchomiony lokalnie, można użyć.

Za pomocą tych usług dostarczania poczty e-mail nie jest ograniczone na platformie Azure, niezależnie od typu subskrypcji.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Dla użytkowników Enterprise Agreement platformy Azure nie ma zmian w technicznej możliwość wysyłania wiadomości e-mail bez użycia uwierzytelnionego przekazywania. Istniejących i nowych użytkowników umowy Enterprise Agreement, można spróbować dostarczania wychodzących wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń z platformą Azure. Mimo że nie gwarantuje, że dostawcy poczty e-mail będą akceptować przychodzących wiadomości e-mail od określonego użytkownika, kolejnymi próbami dostarczenia nie będą blokowane przez platformę Azure dla maszyn wirtualnych w ramach subskrypcji umowy Enterprise Agreement. Musisz pracować bezpośrednio z dostawców poczty e-mail, aby naprawić wszelkie dostarczanie komunikatów lub SPAMU filtrowania problemy, które obejmują określonych dostawców.

## <a name="pay-as-you-go"></a>Płatność zgodnie z rzeczywistym użyciem
Jeśli rejestracji w usłudze 15 listopada 2017 r. płatności lub oferty subskrypcji sieci Microsoft Partner Network, będzie istniało żadne zmiany techniczne możliwości do wypróbowania dostarczania wychodzących wiadomości e-mail. Nadal będzie można spróbować dostarczania wychodzących wiadomości e-mail z maszyn wirtualnych platformy Azure w ramach tych subskrypcji bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń z platformą Azure. Ponownie nie ma żadnej gwarancji że dostawcy poczty e-mail będą akceptować przychodzących wiadomości e-mail od określonego użytkownika, a użytkownicy będą musieli współpracować bezpośrednio z dostawców poczty e-mail, aby naprawić wszelkie dostarczanie komunikatów lub SPAMU filtrowania problemy, które obejmują określonych dostawców.

W przypadku subskrypcji płatności lub Microsoft Partner Network, utworzonych po 15 listopada 2017 r. będą ograniczenia techniczne blokujące wiadomości e-mail, który jest wysyłany bezpośrednio z maszyn wirtualnych w ramach tych subskrypcji. Jeśli ma możliwość wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz zgłosić wniosek o usunięcie ograniczenia. Żądania będą przeglądane i zatwierdzane według uznania firmy Microsoft, a ich zostanie przyznana tylko wtedy, gdy są wprowadzane dodatkowe kontrole na celu zapobieganie oszustwom. Aby utworzyć żądanie, należy otworzyć zgłoszenie do pomocy technicznej przy użyciu następującego typu problemu: **Techniczne** > **sieci wirtualnej** > **łączności** > **nie mogą wysyłać wiadomości e-mail (SMTP/Port 25)**. Upewnij się, że dodano szczegółowe informacje o tym, dlaczego ma wdrożenia do wysyłania wiadomości e-mail bezpośrednio do dostawców poczty, zamiast korzystać z uwierzytelnionego przekazywania.

Gdy subskrypcja płatności lub Microsoft Partner Network czy jest wyłączone, maszyn wirtualnych w ramach tej subskrypcji tylko będą zwolnione przyszłości.

> [!NOTE]
> Firma Microsoft zastrzega sobie prawo do odwołania to wykluczenie, jeśli okaże się, że nastąpiło naruszenie warunków korzystania z usługi.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure — dostęp próbny, Azure in Open, Education, BizSpark i bezpłatna wersja próbna
Jeśli utworzono MSDN, Azure — dostęp próbny, Azure w Open, Education, BizSpark, dostępu Sponsorowanego Azure, platformy Azure dla uczniów, bezpłatnej wersji próbnej lub dowolnej subskrypcji programu Visual Studio po 15 listopada 2017 r., będziesz mieć ograniczenia techniczne tego bloku adresu e-mail, wysyłany z maszyny wirtualnej w ramach tych Subskrypcje bezpośrednio do wiadomości e-mail dostawców. Ograniczenia są gotowe, aby zapobiec nadużyciu. Nie można usunąć tego ograniczenia będzie można przydzielić.

Jeśli używasz tych typów subskrypcji, możesz korzystać z usług przekazywania SMTP, zgodnie z opisem we wcześniejszej części tego artykułu.

## <a name="cloud-service-provider-csp"></a>Dostawca usług w chmurze (CSP)

Jeśli używasz zasobów platformy Azure za pośrednictwem dostawcy usług Kryptograficznych, jesteś w stanie utworzyć zgłoszenie do pomocy technicznej za pośrednictwem wybranego dostawcy usług kryptograficznych i możesz poprosić, aby utworzyć przypadek odblokowanie w Twoim imieniu, jeśli bezpieczne przekazywania SMTP nie można użyć dostawcy CSP.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
