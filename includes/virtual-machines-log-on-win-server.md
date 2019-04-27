---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 05/09/2018
ms.date: 06/04/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9c4910d9ec1fdf651b1bc29955a4e1de2a775f7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576156"
---
1. Kliknij przycisk **Połącz** na stronie właściwości maszyny wirtualnej. 
2. W **Połącz z maszyną wirtualną** strony, zachować, wybierz odpowiednie opcje i kliknij przycisk **pliku RDP Pobierz**.
2. Otwórz pobrany plik RDP i kliknij **Połącz**, gdy wyświetli się odpowiedni monit. 
2. Zostanie wyświetlone ostrzeżenie, że plik `.rdp` pochodzi od nieznanego wydawcy. Jest to normalne. W oknie pulpitu zdalnego kliknij przycisk **Połącz**, aby kontynuować.

    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz poświadczenia konta na maszynie wirtualnej, a następnie kliknij przycisk **OK**.

     **Konto lokalne** — zazwyczaj jest to nazwa użytkownika konta lokalnego i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  

    **Maszyna wirtualna przyłączona do domeny** — jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domena*&#92;*nazwa_użytkownika*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.

    **Kontroler domeny** — jeśli maszyna wirtualna jest kontrolerem domeny, wpisz nazwę użytkownika i hasło konta administratora danej domeny.
4. Kliknij przycisk **Tak**, aby potwierdzić tożsamość maszyny wirtualnej i zakończyć logowanie.

   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/virtual-machines-log-on-win-server/cert-warning.png)
