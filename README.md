**Waves J**
===========

**Описание библиотеки**
-----------------------

Развернуть

Библиотека, написанная на Java, позволяющая взаимодействовать с блокчейном Waves.  
Она предоставляет собой набор готовых инструментов, которые позволяют:

* Взаимодействовать с нодой Waves;
* Работать с транзакциями (токенизация, переводы, лизинг).

**Содержание**
--------------

Развернуть

**Основные возможности  
**

**Начало работы**

* **Требования**
* **Установка**

**Использование**

* ****Создание клиента для ноды****
* ****Создание нового аккаунта****
* ****Транзакция выпуска токена  
    ****
* ****Транзакция перевода  
    ****

**Основные возможности**
------------------------

Развернуть

Полная документация доступна на основном сайте [Documentation](https://example.com/)
------------------------------------------------------------------------------------

**Начало работы**
-----------------

### \- Требования

Развернуть

Версия JDK 1.8+

\- Установка

Развернуть

Для добавления библеотки WavesJ в ваш проект возможны следующие вариации:

##### Maven:

    <dependency>
        <groupId>com.wavesplatform</groupId>
        <artifactId>wavesj</artifactId>
        <version>1.4.1</version>
    </dependency>
    

[Ссылка на библиотеку на портале Maven Central.](https://search.maven.org/artifact/com.wavesplatform/wavesj)

##### Gradle:

    compile group: 'com.wavesplatform', name: 'wavesj', version: '1.4.1'
    

##### SBT:

    libraryDependencies += "com.wavesplatform" % "wavesj" % "1.4.1"
    

### **Использование**

Развернуть

#### В данном примере мы продемонстрируем возможности библиотеки с заведением.**  
  
Создание клиента для ноды **

Сначала необходимо создать клиент для сети блокчейна.  
Выбираем одну из сетей: TESTNET, STAGENET или MAINNET.  
Подробнее о [Сетях блокчейна: Mainnet, Testnet, Stagenet](https://docs.waves.tech/ru/blockchain/blockchain-network/)  
Генерация бесплатных токенов в тестовой сети через [Faucet](https://testnet.wavesexplorer.com/faucet).

**Создаем клиент для ноды теснета**

[?](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932#)

`Node node =` `new` `Node(Profile.TESTNET);System.out.println(``"Current height is "` `+ node.getHeight()); ``// Получаем текущую высоту блокчейна`

#### **Создание нового аккаунта**

**Создаем клиент для ноды теснета**

[?](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932#)

`String seed = Crypto.getRandomSeedPhrase()` `// Генерируется автоматическая seed-фраза`

`PrivateKey alice = PrivateKey.fromSeed(seed);` `// Создается приватный ключ из seed-фразы для пользователя alice`

`PublicKey publicKey = PublicKey.from(alice);` `// Генерируется публичный ключ на основе приватного ключого`

`Address address = Address.from(publicKey)` `// Создается адрес кошелька из публичного ключа`

Учитывая особенности языка Java, возможны два способа реализации функций: через builder или constructor.  
Для каждого из методов транзакций ниже будут продемонстрированы варианты написания двумя способами.  
Подробнее о [транзакциях](https://docs.waves.tech/ru/blockchain/transaction/).

#### **Транзакция выпуска токена**

Функция позволяет создать новый[токен](https://docs.waves.tech/ru/blockchain/token/).  
Подробнее о [транзакции выпуска](https://docs.waves.tech/ru/blockchain/transaction-type/issue-transaction#%D0%BA%D0%BE%D0%BC%D0%B8%D1%81%D1%81%D0%B8%D1%8F-%D0%B7%D0%B0-%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D1%8E).

**Создаем клиент для ноды теснета**

[?](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932#)

`// 1) IssueTransaction (Builder creation)`

`// Проволим транзакцию создания ассета с названием Asset в количесвте 1000 штук и двумя знаками после запятой`

`IssueTransaction tx = IssueTransaction.builder(``"Asset"``,` `1000``,` `2``)`

`.getSignedWith(alice);` `// подписываем транзакцию приватный ключем пользователя alice`

`//  2) IssueTransaction (Constructor creation)`

`IssueTransaction tx =` `new` `IssueTransaction(`

`alice.publicKey(),` `// Получаем публичный ключ пользователя alice`

`"Asset"``,` `// Вводим название создаваемого ассета`

`"description"``,` `// Создаем описание ассета`

`1000``,` `// Задаем выпускаемое количество ассета`

`2``,` `// Указываем количество знаков после запятой`

`false``,` `// Определяем перевыпускаемость токена`

`null``)` `// compileScript - Уточнить!!!!!!!!!!!!!!!!!!!!!!!!!`

`.addProof(alice);` `// подписываем транзакцию приватным ключом пользователя alice`

`// Публикуем транзакцию и дожидаемся, пока она попадет в блокчейн`

`node.waitForTransaction(node.broadcast(tx).id());`

`// Пример созданной транзакции DmpqeTZdXwrxPymZhn9nVBjgnACeXLWyJvriouFaBTc3 на сети TESTNET`

`//Получаем данные о транзакции из ноды`

`IssueTransactionInfo txInfo = node.getTransactionInfo(tx.id(), IssueTransactionInfo.``class``);`

`System.out.println(``"type:"` `+ txInfo.tx().type());`

`System.out.println(``"id:"` `+ txInfo.tx().id());`

`System.out.println(``"fee:"` `+ txInfo.tx().fee().value());`

`System.out.println(``"feeAssetId:"` `+ txInfo.tx().fee().assetId().encoded());`

`System.out.println(``"timestamp:"` `+ txInfo.tx().timestamp());`

`System.out.println(``"version:"` `+ txInfo.tx().version());`

`System.out.println(``"chainId:"` `+ txInfo.tx().chainId());`

`System.out.println(``"sender:"` `+ txInfo.tx().sender().address().encoded());`

`System.out.println(``"senderPublicKey:"` `+ txInfo.tx().sender().encoded());`

`System.out.println(``"proofs:"` `+ txInfo.tx().proofs());`

`System.out.println(``"assetId:"` `+ txInfo.tx().assetId().encoded());`

`System.out.println(``"name:"` `+ txInfo.tx().name());`

`System.out.println(``"quantity:"` `+ txInfo.tx().quantity());`

`System.out.println(``"reissuable:"` `+ txInfo.tx().reissuable());`

`System.out.println(``"decimals:"` `+ txInfo.tx().decimals());`

`System.out.println(``"description:"` `+ txInfo.tx().description());`

`System.out.println(``"script:"` `+ txInfo.tx().script().encoded());`

`System.out.println(``"height:"` `+ txInfo.height());`

`System.out.println(``"applicationStatus:"` `+ txInfo.applicationStatus());`

#### **Транзакция перевода**

Функция выполняет перевод указанного количества [токена](https://docs.waves.tech/ru/blockchain/token/) на другой аккаунт.  
Подробнее о [транзакции перевода](https://docs.waves.tech/ru/blockchain/transaction-type/transfer-transaction#%D0%BA%D0%BE%D0%BC%D0%B8%D1%81%D1%81%D0%B8%D1%8F-%D0%B7%D0%B0-%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D1%8E).

**Создаем клиент для ноды теснета**

[?](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932#)

`// 1) Transfer transaction (Builder creation)`

`TransferTransaction tx = TransferTransaction`

`.builder(`

`bob.address(),` `// Указываем в качестве получателя пользователя bob, например, PrivateKey bob = PrivateKey.fromSeed("some seed 2")`

`Amount.of(``1000``,` `null``)` `// Устанавливаем перечисляемое количество ассета и его assetId.`

`// Для токена Waves assetId равен null. Id кастомных токенов может выглядеть следующим образом FLxekbL91NAZBQodwhASyegg3fWrrsnEe2tLwe1YVoMd.`

`// Например, Amount.of(10, new AssetId("FLxekbL91NAZBQodwhASyegg3fWrrsnEe2tLwe1YVoMd"))`

`)`

`.getSignedWith(alice);` `// подписываем транзакцию приватным ключом пользователя alice`

`// 2) Transfer transaction (Constructor creation)`

`TransferTransaction tx =` `new` `TransferTransaction(`

`alice.publicKey(),` `// Получаем публичный ключ пользователя alice`

`bob.address(),` `// Указываем в качестве получателя пользователя bob, например, PrivateKey bob = PrivateKey.fromSeed("some seed 2")`

`Amount.of(``1000``),` `// Выставляем количество перечисляемого токена`

`null` `// attachment Уточнить!!!!!!!!!!!!!!!!!!!!!!!!!`

`)`

`.addProof(alice);` `// Подписываем транзакцию приватным ключом пользователя alice`

`// Публикуем транзакцию и дожидаемся, пока она попадет в блокчейн`

`node.waitForTransaction(node.broadcast(tx).id());`

`// Получаем данные о транзакции из ноды`

`TransferTransactionInfo txInfo = node.getTransactionInfo(tx.id(), TransferTransactionInfo.``class``);`

`System.out.println(``"type:"` `+ txInfo.tx().type());`

`System.out.println(``"id:"` `+ txInfo.tx().id());`

`System.out.println(``"fee:"` `+ txInfo.tx().fee().value());`

`System.out.println(``"feeAssetId:"` `+ txInfo.tx().fee().assetId().encoded());`

`System.out.println(``"timestamp:"` `+ txInfo.tx().timestamp());`

`System.out.println(``"version:"` `+ txInfo.tx().version());`

`System.out.println(``"chainId:"` `+ txInfo.tx().chainId());`

`System.out.println(``"sender:"` `+ txInfo.tx().sender().address().encoded());`

`System.out.println(``"senderPublicKey:"` `+ txInfo.tx().sender().encoded());`

`System.out.println(``"proofs:"` `+ txInfo.tx().proofs());`

`System.out.println(``"recipient:"` `+ txInfo.tx().recipient());`

`System.out.println(``"assetId:"` `+ txInfo.tx().amount().assetId().encoded());`

`System.out.println(``"amount:"` `+ txInfo.tx().amount().value());`

`System.out.println(``"attachment:"` `+ txInfo.tx().attachment().encoded());`

`System.out.println(``"height:"` `+ txInfo.height());`

`System.out.println(``"applicationStatus:"` `+ txInfo.applicationStatus());`

\- Errors  

Развернуть

  

_For more examples, please refer to the [Documentation](https://example.com/)_

[Like](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932)Be the first to like this

* No labels
* [Edit Labels](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932# "Edit Labels")

[![User icon: Add a picture of yourself](./GitHub ReadME WJ (RU) - Tech Docs Templates - Waves Confluence_files/add_profile_pic.svg)](https://confluence.wavesplatform.com/users/profile/editmyprofilepicture.action)

Write a comment…

[Add Comment](https://confluence.wavesplatform.com/pages/viewpage.action?pageId=1696237932&showComments=true&showCommentArea=true#addcomment)
