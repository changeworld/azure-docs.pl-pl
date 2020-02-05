---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konfigurowanie systemu DNS do automatycznej synchronizacji chmury prywatnej
description: Opisuje sposób konfigurowania rozpoznawania nazw DNS w celu uzyskania dostępu do programu vCenter Server w chmurze prywatnej o automatycznej synchronizacji z lokalnej stacji roboczej
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eadedcea7e6010cf93d118b3781630053609d29f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019608"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurowanie systemu DNS do rozpoznawania nazw w celu automatycznej synchronizacji dostępu do programu vCenter w chmurze prywatnej z poziomu lokalnych stacji roboczych

Aby uzyskać dostęp do serwera vCenter w chmurze prywatnej o automatycznej synchronizacji z lokalnych stacji roboczych, należy skonfigurować rozpoznawanie adresów DNS, aby serwer vCenter mógł zostać rozkierowany przy użyciu nazwy hosta i adresu IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>Uzyskaj adres IP serwera DNS dla chmury prywatnej do automatycznej synchronizacji

1. Zaloguj się do [portalu automatycznej](access-cloudsimple-portal.md)rejestracji.

2. Przejdź do **zasobów** > **automatycznej synchronizacji chmur prywatnych** i wybierz chmurę prywatną do automatycznej synchronizacji, z którą chcesz nawiązać połączenie.

3. Na stronie **Podsumowanie** chmury prywatnej automatycznej synchronizacji w sekcji **podstawowe informacje**Skopiuj adres IP serwera DNS w chmurze automatycznej synchronizacji.

    ![Automatyczna synchronizacja serwerów DNS w chmurze prywatnej](media/private-cloud-dns-server.png)


Użyj dowolnej z tych opcji dla konfiguracji DNS.

* [Utwórz strefę na serwerze DNS dla *. AVS.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Utwórz usługę przesyłania dalej warunkowego na lokalnym serwerze DNS, aby rozwiązać ten problem *. AVS.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-avsio"></a>Utwórz strefę na serwerze DNS dla *. AVS.io

Można skonfigurować strefę jako strefę zastępczą i wskazać serwery DNS w chmurze prywatnej do rozpoznawania nazw. Ta sekcja zawiera informacje dotyczące korzystania z serwera DNS BIND lub serwera DNS systemu Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Tworzenie strefy na serwerze DNS BIND

Określony plik i parametry do skonfigurowania mogą się różnić w zależności od konfiguracji poszczególnych ustawień DNS.

Na przykład dla domyślnej konfiguracji serwera BIND Edytuj plik/etc/named.conf na serwerze DNS i Dodaj następujące informacje o strefie.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Tworzenie strefy na serwerze DNS systemu Microsoft Windows

1. Kliknij prawym przyciskiem myszy serwer DNS i wybierz pozycję **Nowa strefa**. 
  
    ![Nowa strefa](media/DNS01.png)
2. Wybierz pozycję **strefa zastępcza** i kliknij przycisk **dalej**.

    ![Nowa strefa](media/DNS02.png)
3. Wybierz odpowiednią opcję w zależności od środowiska, a następnie kliknij przycisk **dalej**.

    ![Nowa strefa](media/DNS03.png)
4. Wybierz pozycję **Strefa wyszukiwania do przodu** , a następnie kliknij przycisk **dalej**.

    ![Nowa strefa](media/DNS01.png)
5. Wprowadź nazwę strefy, a następnie kliknij przycisk **dalej**.

    ![Nowa strefa](media/DNS05.png)
6. Wprowadź adresy IP serwerów DNS dla swojej chmury prywatnej automatycznej wersji zastosowanej w portalu do automatycznej synchronizacji.

    ![Nowa strefa](media/DNS06.png)
7. W razie potrzeby kliknij przycisk **dalej** , aby zakończyć pracę Instalatora kreatora.

## <a name="create-a-conditional-forwarder"></a>Tworzenie usługi przesyłania dalej warunkowego

Usługa przesyłania dalej warunkowego przekazuje wszystkie żądania rozpoznawania nazw DNS do wywskazanego serwera. W przypadku tej konfiguracji każde żądanie do *. AVS.io jest przekazywany do serwerów DNS znajdujących się w chmurze prywatnej automatycznej synchronizacji. W poniższych przykładach pokazano, jak skonfigurować usługi przesyłania dalej na różnych typach serwerów DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Tworzenie usługi przesyłania dalej warunkowej na serwerze DNS BIND

Określony plik i parametry do skonfigurowania mogą się różnić w zależności od konfiguracji poszczególnych ustawień DNS.

Na przykład dla domyślnej konfiguracji serwera BIND Edytuj plik/etc/named.conf na serwerze DNS i Dodaj następujące informacje warunkowego przekazywania.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Tworzenie usługi przesyłania dalej warunkowej na serwerze DNS systemu Microsoft Windows

1. Otwórz Menedżera DNS na serwerze DNS.
2. Kliknij prawym przyciskiem myszy pozycję **usługi przesyłania dalej warunkowe** i wybierz opcję dodania nowej usługi przesyłania dalej warunkowego.

    ![Warunkowy serwer usługi przesyłania dalej 1 systemu Windows](media/DNS08.png)
3. Wprowadź domenę DNS i adres IP serwerów DNS w chmurze prywatnej automatycznej synchronizacji, a następnie kliknij przycisk **OK**.
