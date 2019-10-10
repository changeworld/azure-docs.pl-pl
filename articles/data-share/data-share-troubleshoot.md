---
title: Rozwiązywanie problemów z podglądem udziału danych platformy Azure
description: Dowiedz się, jak rozwiązywać problemy z zaproszeniami i błędami podczas tworzenia lub otrzymywania udziałów danych za pomocą usługi Azure Data Share Preview.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 592a2d464aed8c39dfd11734beccbd0399d75fd9
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169227"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Rozwiązywanie typowych problemów w wersji zapoznawczej usługi Azure Data Share

W tym artykule pokazano, jak rozwiązywać typowe problemy z usługą Azure Data Share Preview. 

## <a name="azure-data-share-invitations"></a>Zaproszenia udziału danych platformy Azure 

W niektórych przypadkach, gdy nowy użytkownik kliknie opcję **Zaakceptuj zaproszenie** z zaproszenia e-mail, które zostało wysłane, może zostać wyświetlona pusta lista zaproszeń. 

![Brak zaproszeń](media/no-invites.png)

Powyższy błąd to znany problem z usługą i aktualnie trwa jej rozwiązywanie. Aby obejść ten krok, wykonaj poniższe czynności. 

1. W Azure Portal przejdź do **subskrypcji**
1. Wybierz subskrypcję używaną dla udziału danych platformy Azure
1. Kliknij pozycję **dostawcy zasobów**
1. Wyszukaj w usłudze Microsoft. datashare
1. Kliknij pozycję **zarejestruj** .

Aby wykonać te kroki, musisz mieć [rolę RBAC współautor platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) . 

Jeśli nadal nie możesz zobaczyć zaproszenia do udziału danych, skontaktuj się z dostawcą danych i upewnij się, że przesłały zaproszenie do adresu e-mail logowania platformy Azure, a *nie* aliasu e-mail. 

> [!IMPORTANT]
> Jeśli zaakceptujesz już zaproszenie do udziału danych platformy Azure i zakończysz działanie usługi przed rozpoczęciem konfigurowania magazynu, postępuj zgodnie z instrukcjami szczegółowymi w temacie [Konfigurowanie mapowania zestawu danych](how-to-configure-mapping.md) — Przewodnik po tym, aby dowiedzieć się, jak zakończyć konfigurowanie otrzymanego udziału danych i zacznij odbierać dane. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Błąd podczas tworzenia lub otrzymywania nowego udziału danych

"Błąd: operacja zwróciła nieprawidłowy kod stanu" nieprawidłowego żądania ""

"Błąd: AuthorizationFailed"

"Błąd: przypisanie roli do konta magazynu"

![Błąd uprawnień](media/error-write-privilege.png)

Jeśli podczas tworzenia nowego udziału danych lub odbierania nowego udziału danych pojawi się jeden z powyższych błędów, oznacza to brak wystarczających uprawnień do konta magazynu. Wymagane uprawnienie to *Microsoft. Authorization/przypisania ról/zapis*, które istnieje w roli właściciela magazynu lub można przypisać do roli niestandardowej. Nawet jeśli utworzysz konto usługi Storage, NIE sprawi to, że automatycznie staniesz się właścicielem konta magazynu. Wykonaj poniższe kroki, aby przyznać sobie rolę właściciela konta magazynu. Alternatywnie można utworzyć rolę niestandardową z tym uprawnieniem, które można dodać samodzielnie do programu.  

1. Przejdź do konta usługi Storage w witrynie Azure Portal
1. Wybieranie **kontroli dostępu (IAM)**
1. Kliknij przycisk **Dodaj**
1. Dodaj siebie jako właściciela.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .

