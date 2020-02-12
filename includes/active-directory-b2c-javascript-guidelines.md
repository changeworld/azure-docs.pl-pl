---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149080"
---
## <a name="guidelines-for-using-javascript"></a>Wytyczne dotyczące przy użyciu języka JavaScript

Podczas dostosowywania interfejsu aplikacji przy użyciu języka JavaScript, należy przestrzegać następujących wytycznych:

- Nie należy wiązać zdarzenia kliknięcia dla `<a>` elementów HTML.
- Nie wykona zależności kodu usługi Azure AD B2C lub komentarzy.
- Zmienianie kolejności lub hierarchia elementów HTML w usłudze Azure AD B2C. Można określić kolejność elementów interfejsu użytkownika, należy użyć zasad usługi Azure AD B2C.
- Można wywołać dowolną usługę RESTful za pomocą tych zagadnień:
    - Może być konieczne ustawienie usługi RESTful CORS, aby umożliwić połączeń HTTP klienta.
    - Upewnij się, że usługi RESTful są bezpieczne i korzystają z protokołu HTTPS.
    - Nie bezpośrednio za pomocą języka JavaScript wywoływanie punktów końcowych usługi Azure AD B2C.
- Możesz osadzić Twój kod JavaScript, lub można połączyć z zewnętrznych plików JavaScript. Korzystając z zewnętrznego pliku JavaScript, upewnij się użyć bezwzględnego adresu URL i nie względnym adresem URL.
- Struktury języka JavaScript:
    - Usługa Azure AD B2C używa określonej wersji jQuery. Nie dołączaj inna wersja jQuery. Na tej samej stronie przy użyciu więcej niż jedna wersja powoduje problemy.
    - Za pomocą RequireJS nie jest obsługiwane.
    - Większość platform JavaScript nie są obsługiwane przez usługę Azure AD B2C.
- Ustawienia Azure AD B2C mogą być odczytywane przez wywoływanie `window.SETTINGS`, `window.CONTENT` obiektów, takich jak bieżący język interfejsu użytkownika. Nie zmieniaj wartości tych obiektów.
- Aby dostosować komunikat o błędzie usługi Azure AD B2C, należy użyć lokalizacji w zasadach.
- Jeśli wszystko można osiągnąć za pomocą zasad, zazwyczaj jest to zalecany sposób.
