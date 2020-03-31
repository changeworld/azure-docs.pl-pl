---
title: Tworzenie grup adresów IP w Zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP i zarządzanie nimi dla reguł zapory platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444539"
---
# <a name="create-ip-groups-preview"></a>Tworzenie grup IP (wersja zapoznawcza)

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grupy IP umożliwiają grupowanie adresów IP i zarządzanie nimi dla reguł zapory platformy Azure. Mogą mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

## <a name="create-an-ip-group"></a>Tworzenie grupy adresów IP

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. Wpisz **grupy ADRESÓW IP** w polu tekstowym wyszukiwania, a następnie wybierz pozycję Grupy **ADRESÓW IP**.
3. Wybierz **pozycję Utwórz**.
4. Wybierz subskrypcję.
5. Wybierz grupę zasobów lub utwórz nową.
6. Wpisz unikatową nazwę grupy IP, a następnie wybierz region.

6. Wybierz **dalej: adresy IP**.
7. Wpisz adres IP, wiele adresów IP lub zakresów adresów IP.

   Istnieją dwa sposoby wprowadzania adresów IP:
   - Można je wprowadzić ręcznie
   - Można je zaimportować z pliku

   Aby zaimportować z pliku, wybierz pozycję **Importuj z pliku**. Możesz przeciągnąć plik do pola lub wybrać **opcję Przeglądaj pliki**. W razie potrzeby możesz przeglądać i edytować przesłane adresy IP.

   Po wpisaniu adresu IP portal sprawdza jego poprawność, aby sprawdzić, czy nie występują nakładające się, duplikaty i problemy z formatowaniem.

5. Po zakończeniu wybierz **pozycję Recenzja + Utwórz**.
6. Wybierz **pozycję Utwórz**.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o grupach IP](ip-groups.md)