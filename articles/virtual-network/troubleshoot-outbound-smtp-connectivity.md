---
title: Rozwiązywanie problemów z wychodzącą łącznością SMTP na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z wychodzącą łącznością SMTP na platformie Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058935"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Rozwiązywanie problemów z łącznością smtp wychodzących na platformie Azure

Począwszy od 15 listopada 2017 r. wychodzące wiadomości e-mail wysyłane bezpośrednio do domen zewnętrznych (takich jak outlook.com i gmail.com) z maszyny wirtualnej są udostępniane tylko niektórym typom subskrypcji na platformie Microsoft Azure. Połączenia SMTP wychodzące korzystające z portu TCP 25 zostały zablokowane. (Port 25 jest używany głównie do nieuwierzyleni dostarczania wiadomości e-mail.)

Ta zmiana w zachowaniu dotyczy tylko nowych subskrypcji i nowych wdrożeń od 15 listopada 2017 r.

## <a name="recommended-method-of-sending-email"></a>Zalecana metoda wysyłania wiadomości e-mail
Zaleca się używanie uwierzytelnionych usług przekazywania SMTP (które zazwyczaj łączą się za pośrednictwem portu TCP 587 lub 443, ale obsługują także inne porty) do wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure lub z usług Azure App Services. Usługi te są używane do utrzymania reputacji adresu IP lub domeny, aby zminimalizować możliwość, że zewnętrzni dostawcy poczty e-mail odrzucą wiadomość. Takie usługi przekazywania SMTP obejmują, ale nie są ograniczone do [SendGrid](https://sendgrid.com/partners/azure/). Jest również możliwe, że masz bezpieczną usługę przekazywania SMTP, która jest uruchomiona lokalnie, której można użyć.

Korzystanie z tych usług dostarczania poczty e-mail nie jest ograniczone na platformie Azure, niezależnie od typu subskrypcji.

## <a name="enterprise-agreement"></a>Enterprise Agreement
W przypadku użytkowników platformy Azure z umową Enterprise Agreement nie ma żadnych zmian w technicznej możliwości wysyłania wiadomości e-mail bez użycia uwierzytelnionego przekazywania. Zarówno nowi, jak i istniejący użytkownicy umowy Enterprise Agreement mogą próbować wychodzącego dostarczania wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń z platformy Azure. Chociaż nie ma gwarancji, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail od danego użytkownika, próby dostarczenia nie będą blokowane przez platformę Azure dla maszyn wirtualnych w ramach subskrypcji umowy Enterprise Agreement. Musisz pracować bezpośrednio z dostawcami poczty e-mail, aby rozwiązać wszelkie problemy z dostarczaniem wiadomości lub filtrowaniem spamu, które dotyczą określonych dostawców.

## <a name="pay-as-you-go"></a>Płatność zgodnie z rzeczywistym użyciem
Jeśli zarejestrowałeś się przed 15 listopada 2017 r. w przypadku ofert subskrypcji płatności zgodnie z rzeczywistym użyciem lub sieci partnerów firmy Microsoft, nie będzie żadnych zmian w technicznej możliwości wypróbowania wychodzącego dostarczania wiadomości e-mail. Nadal będziesz mieć możliwość wypróbowania wychodzącego dostarczania wiadomości e-mail z maszyn wirtualnych platformy Azure w ramach tych subskrypcji bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń z platformy Azure. Ponownie, nie ma gwarancji, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail od danego użytkownika, a użytkownicy będą musieli współpracować bezpośrednio z dostawcami poczty e-mail, aby rozwiązać wszelkie problemy z dostarczaniem wiadomości lub filtrowaniem spamu, które dotyczą określonych dostawców.

W przypadku subskrypcji płatności zgodnie z rzeczywistym lub siecią partnerów firmy Microsoft utworzonych po 15 listopada 2017 r. będą istnieć ograniczenia techniczne, które blokują wiadomości e-mail wysyłane bezpośrednio z maszyn wirtualnych w ramach tych subskrypcji. Jeśli chcesz mieć możliwość wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail (nie przy użyciu uwierzytelnionego przekazywania SMTP), możesz wysłać żądanie usunięcia ograniczenia. Żądania będą sprawdzane i zatwierdzane według uznania firmy Microsoft, a będą one przyznawane tylko po dokonaniu dodatkowych kontroli zwalczania nadużyć finansowych. Aby wysłać żądanie, otwórz zgłoszenie pomocy technicznej przy użyciu następującego typu problemu: **Techniczne** >  > **połączenie** > **sieci wirtualnej****nie może wysłać poczty e-mail (SMTP/Port 25)**. Upewnij się, że dodajesz szczegółowe informacje o tym, dlaczego wdrożenie musi wysyłać pocztę bezpośrednio do dostawców poczty, a nie przy użyciu uwierzytelnionego przekazywania.

Po pay-as-you-go lub microsoft partner network subskrypcji jest zwolniony i maszyny wirtualne zostały "zatrzymane" & "Uruchomiono" z witryny Azure portal, wszystkie maszyny wirtualne w ramach tej subskrypcji zostaną wyłączone w przyszłości. Zwolnienie ma zastosowanie tylko do żądanej subskrypcji i dotyczy tylko ruchu maszyny wirtualnej kierowanego bezpośrednio do Internetu. Ruch portu routingu 25 za pośrednictwem usług PaaS platformy Azure, takich jak [Zapora azure,](https://azure.microsoft.com/services/azure-firewall/) nie jest obsługiwany.

> [!NOTE]
> Firma Microsoft zastrzega sobie prawo do odwołania tego zwolnienia, jeśli stwierdzi, że doszło do naruszenia warunków świadczenia usług.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure w wersji otwartej, edukacyjnej, BizSpark i bezpłatnej wersji próbnej
Jeśli po 15 listopada 2017 r. utworzono usługę MSDN, azure pass, platformę Azure w wersji Open, Education, BizSpark, platformie Azure Sponsorship, Azure Student, bezpłatna wersja próbna lub dowolną subskrypcję programu Visual Studio, ograniczenia techniczne blokują wiadomości e-mail wysyłane z maszyn wirtualnych w ramach tych subskrypcji bezpośrednio do dostawców poczty e-mail. Ograniczenia są wykonywane w celu zapobiegania nadużyciom. Żadne wnioski o usunięcie tego ograniczenia nie zostaną przyznane.

Jeśli używasz tych typów subskrypcji, zachęcamy do korzystania z usług przekazywania SMTP, jak opisano wcześniej w tym artykule lub zmienić typ subskrypcji.

## <a name="cloud-service-provider-csp"></a>Dostawca usług w chmurze (CSP)

Jeśli używasz zasobów platformy Azure za pośrednictwem usługi CSP, możesz zażądać od zabezpieczenia żądania zwolnienia z firmą Microsoft w Twoim imieniu, jeśli nie można użyć bezpiecznego przekaźnika SMTP.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem, używając następującego typu problemu: Typ problemu z **zarządzaniem subskrypcją:** **Żądanie włączenia przepływu poczty e-mail portu 25**.
