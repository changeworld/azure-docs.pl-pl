---
title: JavaScript i strony umowy wersje przepływy użytkownika w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć język JavaScript i użyć wersji kontraktu stron, aby dostosować przepływ użytkownika w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401555"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>O wersji JavaScript i strony umowy w ramach przepływu użytkownika

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Usługa Azure AD B2C zawiera zbiór zawartość pakietu zawierający elementy interfejsu użytkownika w swoich przepływach użytkowników HTML, CSS i JavaScript. Jeśli zamierzasz włączyć [JavaScript](javascript-samples.md) przepływów kodu po stronie klienta w użytkowników, należy mieć pewność, że elementy Twój kod JavaScript jest tworzony na są niezmienne. W przeciwnym razie zmiany może spowodować nieoczekiwane zachowanie na użytkownika strony przepływu. Aby uniknąć tych problemów, można wymusić użycie kontraktu strony dla przepływu użytkownika i określić wersję strony umowy. W ten sposób będzie upewnij się, że wszystkie definicje zawartości, które Twój kod JavaScript jest oparty na niezmienne. Nawet jeśli nie zamierzasz włączyć język JavaScript dla przepływu użytkownika, można określić wersja kontraktu strony stron przepływu użytkownika.

> [!NOTE]
> W tym artykule omówiono JavaScript dla przepływów użytkownika, ale można również użyć języka JavaScript i wybierz wersje kontraktu stron, podczas korzystania z [zasady niestandardowe](page-contract.md).

## <a name="enable-javascript"></a>Włącz język JavaScript

W oknie właściwości przepływu użytkownika można włączyć kodu JavaScript, który wymusza także użycie kontraktu strony. Następnie można ustawić wersja kontraktu strony, zgodnie z opisem w następnej sekcji.

![Włącz ustawienie języka JavaScript](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Określ wersję kontraktu strony

Czy JavaScript zostanie włączone we właściwościach przepływ użytkownika, można określić wersja kontraktu strony stron przepływu użytkownika. Otwórz przepływ użytkownika, a następnie wybierz pozycję **układy stron**. W obszarze **Nazwa układu**, wybierz stronę przepływ użytkownika i wybierz **wersja kontraktu strony**.

![Włącz ustawienie języka JavaScript](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>Kolejne kroki
Zobacz [przykładowe skrypty JavaScript do użycia w usłudze Azure Active Directory B2C](javascript-samples.md).
