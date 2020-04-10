---
title: 'Szybki start: tworzenie aplikacji języka Python — usługa Azure Cache for Redis'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację języka Python, która używa usługi Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 6ce3dd1cbb694988af3555765342a1c4ca3850b5
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010855"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Szybki start: tworzenie aplikacji języka Python, która używa usługi Azure Cache for Redis

W tym artykule włączysz usługę Azure Cache for Redis do aplikacji języka Python, aby mieć dostęp do bezpiecznej, dedykowanej pamięci podręcznej dostępnej z dowolnej aplikacji na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Python 2 lub 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalowanie klienta redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) to interfejs języka Python dla usługi Azure Cache for Redis. Użyj narzędzia Pakiety Python, *pip*, aby zainstalować pakiet *redis-py* z wiersza polecenia. 

W poniższym przykładzie *użyto pip3* dla języka Python 3 do *zainstalowania redis-py* w systemie Windows 10 z wiersza polecenia administratora.

![Instalowanie interfejsu redis-py Python w pamięci podręcznej platformy Azure dla programu Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Odczytywanie i zapisywanie w pamięci podręcznej

Uruchom pythona z wiersza polecenia i przetestować pamięć podręczną przy użyciu następującego kodu. Zamień `<Your Host Name>` i `<Your Access Key>` wartości z usługi Azure Cache dla wystąpienia Redis. Nazwa hosta jest * \<nazwą DNS formularza>.redis.cache.windows.net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> W przypadku usługi Azure Cache dla programu Redis w wersji 3.0 lub nowszej sprawdzanie certyfikatów TLS/SSL jest wymuszane. ssl_ca_certs musi być jawnie ustawiona podczas łączenia się z usługą Azure Cache for Redis. W przypadku RedHat Linux ssl_ca_certs znajdują się w module certyfikatu */etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Tworzenie przykładowej aplikacji języka Python

Utwórz nowy plik tekstowy, dodaj następujący skrypt i zapisz plik jako *PythonApplication1.py*. Zamień `<Your Host Name>` i `<Your Access Key>` wartości z usługi Azure Cache dla wystąpienia Redis. Nazwa hosta jest * \<nazwą DNS formularza>.redis.cache.windows.net*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Uruchom *PythonApplication1.py* z Pythonem. Powinny być widoczne wyniki, takie jak w poniższym przykładzie:

![Uruchamianie skryptu języka Python w celu przetestowania dostępu do pamięci podręcznej](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli skończysz z grupą zasobów platformy Azure i zasobami utworzonymi w tym przewodniku Szybki start, możesz je usunąć, aby uniknąć opłat.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne, a grupa zasobów i wszystkie zasoby w niej znajdują się na stałe. Jeśli utworzono usługę Azure Cache for Redis wystąpienie w istniejącej grupy zasobów, które chcesz zachować, można usunąć tylko pamięci podręcznej, wybierając **Usuń** ze strony **przegląd** pamięci podręcznej. 

Aby usunąć grupę zasobów i jej pamięć podręczną Redis dla wystąpienia platformy Azure:

1. Z [witryny Azure portal](https://portal.azure.com)wyszukaj i wybierz **grup zasobów**.
1. W polu tekstowym **Filtruj według nazwy** wprowadź nazwę grupy zasobów zawierającej wystąpienie pamięci podręcznej, a następnie wybierz ją z wyników wyszukiwania. 
1. Na stronie grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.
   
   ![Usuwanie grupy zasobów dla usługi Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.](./cache-web-app-howto.md)

