---
title: Rozwiązywanie problemów z udziału danych platformy Azure w wersji zapoznawczej
description: Dowiedz się, jak rozwiązywać problemy związane z udziału danych platformy Azure w wersji zapoznawczej
services: data-share
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: c02f72d6a327c4dcb94ac8844005613cfe316986
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838382"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Rozwiązywanie typowych problemów w wersji zapoznawczej udziału danych platformy Azure

W tym artykule pokazano, jak rozwiązywać typowe problemy dla udziału danych platformy Azure w wersji zapoznawczej. 

## <a name="azure-data-share-invitations"></a>Azure zaproszeń do udostępniania danych 

W niektórych przypadkach, gdy nowy użytkownik kliknie **zaakceptować zaproszenie** z zaproszenie e-mail, na który została wysłana, mogą być prezentowane z pustą listą zaproszeń. 

![Brak zaproszeń](media/no-invites.png)

Błąd powyżej jest to znany problem z usługą i jest obecnie rozwiązane. Jako obejście tego problemu, wykonaj poniższe kroki. 

1. W witrynie Azure portal przejdź do **subskrypcji**
1. Wybierz subskrypcję, której używasz dla udziału danych platformy Azure
1. Kliknij pozycję **dostawców zasobów**
1. Wyszukaj Microsoft.DataShare
1. Kliknij przycisk **zarejestrować**

Musisz mieć [rolę RBAC Współautor platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) do wykonania tych kroków. 

Jeśli nadal nie można wyświetlić danych, udostępnianie z zaproszeniem, skontaktuj się z dostawcą danych i upewnij się, że wysłali zaproszenia do adresu e-mail logowania do platformy Azure i *nie* Twój alias e-mail. 

> [!IMPORTANT]
> Jeśli masz już zaakceptowano zaproszenie udostępniania danych platformy Azure i zakończył działanie usługi przed skonfigurowaniem opcji magazynu, postępuj zgodnie z instrukcjami opisanymi w [skonfigurować mapowanie zestawu danych](how-to-configure-mapping.md) przewodnik, aby dowiedzieć się, jak ukończyć konfigurowanie usługi dane odebrane udostępnianie i zaczynają się pojawiać dane. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Błąd podczas tworzenia lub otrzymaniu nowego udziału danych

"Błąd: Operacja zwróciła nieprawidłowy kod stanu "Element BadRequest" "

"Błąd: AuthorizationFailed"

"Błąd: przypisania roli do konta magazynu"

![Błąd uprawnień](media/error-write-privilege.png)

Jeśli pojawi się jeden z powyższych błędów podczas tworzenia nowego udziału danych lub otrzymaniu nowego udziału danych, jest ponieważ ma wystarczających uprawnień do konta magazynu. Wymagane uprawnienie *Microsoft.Authorization/role przypisania/zapis*, który istnieje w roli właściciela magazynu lub mogą być przypisane do roli niestandardowej. Nawet jeśli zostało utworzone konto magazynu, nie automatycznie powoduje ona możesz właściciela konta magazynu. Wykonaj następujące kroki, aby przyznać sobie właściciela konta magazynu. Alternatywnie można utworzyć rolę niestandardową mający to uprawnienie, które można dodać użytkownika do.  

1. Przejdź do konta magazynu w witrynie Azure portal
1. Wybierz **kontrola dostępu (IAM)**
1. Kliknij przycisk **Dodaj**
1. Dodaj siebie w właściciela.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uruchomić, udostępnianie danych, w dalszym ciągu [udostępnianie danych](share-your-data.md) samouczka.

