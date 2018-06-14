---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bef314e60b962e39b3a55d0fb7acb40d4d1b4c32
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30920544"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń z witryny Azure Portal

Aplikacja przykładowa musi uwierzytelnić dostęp do konta magazynu. Na potrzeby uwierzytelniania podajesz aplikacji swoje poświadczenia konta magazynu w postaci parametrów połączenia. Aby wyświetlić swoje poświadczenia konta magazynu:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.   
4. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia. W następnym kroku dodasz wartość parametrów połączenia do zmiennej środowiskowej.

    ![Zrzut ekranu pokazujący sposób kopiowania parametrów połączenia z witryny Azure Portal](media/storage-copy-connection-string-portal/portal-connection-string.png)
