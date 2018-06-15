---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 842083f0f69552fb9076423386353dbb4dae73b5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012817"
---
1. Kliknij przycisk **Connect** przycisk na stronie właściwości maszyny wirtualnej. 
2. W **nawiązywanie połączenia z maszyną wirtualną** , przechowywać, wybierz odpowiednie opcje i kliknij przycisk **plik RDP Pobierz**.
2. Otwórz pobrany plik RDP, a następnie kliknij przycisk **Connect** po wyświetleniu monitu. 
2. Zostanie wyświetlone ostrzeżenie, że plik `.rdp` pochodzi od nieznanego wydawcy. Jest to normalne. W oknie pulpitu zdalnego kliknij przycisk **Połącz**, aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz poświadczenia konta na maszynie wirtualnej, a następnie kliknij przycisk **OK**.
   
     **Konto lokalne** — zazwyczaj jest to nazwa użytkownika konta lokalnego i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny** — jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domena*&#92;*nazwa_użytkownika*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny** — jeśli maszyna wirtualna jest kontrolerem domeny, wpisz nazwę użytkownika i hasło konta administratora danej domeny.
4. Kliknij przycisk **Tak**, aby potwierdzić tożsamość maszyny wirtualnej i zakończyć logowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/virtual-machines-log-on-win-server/cert-warning.png)

