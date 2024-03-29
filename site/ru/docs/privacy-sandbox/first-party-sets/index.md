---
layout: 'layouts/doc-post.njk'
title: Наборы собственных доменов
subhead: Механизм, позволяющий связанным доменным именам, имеющим общего владельца, объявлять принадлежность к одному и тому же источнику.
description: Наборы собственных доменов позволяют объявить принадлежность связанных доменных имен, имеющих общего владельца, к одному и тому же источнику.
date: 2021-05-18
updated: 2021-08-12
authors:
  - samdutton
---

<!--lint disable no-smart-quotes-->

## Статус реализации

- [Доступно в рамках испытания Origin Trial](/docs/web-platform/origin-trials/) в Chrome с 89 по 93.
- [Регистрация для участия в испытании Origin Trial](/origintrials/#/view_trial/988540118207823873).
- [Статус платформы Chrome](https://chromestatus.com/feature/5640066519007232).
- [Проекты Chromium](https://www.chromium.org/updates/first-party-sets).

## Зачем нужны наборы собственных доменов?

{% YouTube id='cNJ8mZ-J3F8' %}

Веб-страницы состоят из контента из различных [источников](/docs/privacy-sandbox/glossary#origin). Некоторый контент является собственным, т. е. загружается с основного сайта, на который зашел посетитель. Другой контент загружается из сторонних источников—это может быть реклама, встраиваемые медиаобъекты, а также общие ресурсы, такие как JavaScript-библиотеки из [сетей доставки контента (CDN)](https://www.cloudflare.com/en-gb/learning/cdn/what-is-a-cdn/). Сторонние источники также могут сопоставлять активность пользователя на различных сайтах, используя такие механизмы, как [файлы cookie](/docs/privacy-sandbox/glossary#origin).

Разработчики браузеров предлагают модели обеспечения конфиденциальности, ограничивающие доступ к личным данным пользователя в межсайтовом контексте. Однако у многих организаций есть связанные между собой сайты, расположенные на различных доменных именах, например версии для разных стран (такие как `example.com` и `example.co.uk`). Необходимо, чтобы доменные имена, связанные определенным образом (например, имеющие общего владельца), могли заявить о своей принадлежности к одному источнику, чтобы в тех случаях, когда происходит разграничение между собственными и сторонними доменами, такие домены воспринимались браузерами как собственные домены сайта.

Кроме того, любое решение должно быть устойчивым к злоупотреблению системой. Например, необходимо исключить возможность добавления организаций, включающих не связанные между собой сайты с разными владельцами, с целью получения привилегий собственного домена.

## Как работают собственные наборы?

Сайт может объявить о том, что является участником (или владельцем) набора доменов, опубликовав файл манифеста, в котором описаны связи с другими доменами. Этот файл имеет формат JSON и располагается по адресу `.well-known/first-party-set`.

Предположим, нам нужно объединить `a.example`, `b.example` и `c.example` в набор собственных доменов, где `a.example` будет выступать в качестве владельца. Для этого сайты должны опубликовать следующие ресурсы:

```json
// https://a.example/.well-known/first-party-set
{
  "owner": "a.example",
  "members": ["b.example", "c.example"],
  ...
}

// https://b.example/.well-known/first-party-set
{
	"owner": "a.example"
}

// https://c.example/.well-known/first-party-set
{
	"owner": "a.example"
}
```

Домен-владелец размещает файл манифеста, в котором перечислены домены участников. Браузер может запросить у сайта-участника информацию о владельце, а затем свериться с манифестом владельца, чтобы подтвердить связь между ними.

Предполагается, что политики браузеров будут предотвращать злоупотребление. Например, наборы собственных доменов не должны допускать обмена пользовательской информацией между не связанными друг с другом сайтами или объединения в группы сайтов с разными владельцами. Один из возможных способов регистрации сайтов—это требовать от них публикации файла с описанием группы доменов в общедоступном трекере (например, в специализированном GitHub-репозитории) вместе с информацией, необходимой для удовлетворения политики браузера. Для каждого из доменов в группе также может потребоваться публикация в каталоге `.well-known` специального файла для подтверждения, что домен действительно находится под управлением владельца группы.

В дополнение к наборам собственных доменов также предлагается ввести атрибут `SameParty` для файлов cookie. Атрибут `SameParty` в файле cookie сообщает браузеру, что действие этого файла должно распространяться на контексты, являющиеся частью того же набора собственных доменов, что и основной контекст.

Например, в случае с описанным выше набором собственных доменов сайт a.example может установить следующий файл cookie:

`Set-Cookie: session=123; Secure; SameSite=Lax; SameParty`

Теперь, если посетитель, находящийся на сайте b.example или c.example, отправит запрос на сайт a.example, этот запрос будет включать файл cookie `session`.

---

## Участвуйте и делитесь отзывами

- **Испытание Origin Trial**: зарегистрируйтесь и примите участие в [испытании Chrome Origin Trial](/origintrials/#/view_trial/988540118207823873).
- **GitHub**: ознакомьтесь с [текстом предложения](https://github.com/privacycg/first-party-sets) и [обсуждением, где можно задать свои вопросы](https://github.com/privacycg/first-party-sets/issues).
- **Поддержка разработчиков**: задавайте вопросы и участвуйте в обсуждениях в [репозитории поддержки разработчиков Privacy Sandbox](https://github.com/GoogleChromeLabs/privacy-sandbox-dev-support).

## Дополнительная информация

- [Техническое описание наборов собственных доменов](https://github.com/privacycg/first-party-sets)
- [Статус платформы Chrome](https://chromestatus.com/feature/5640066519007232).
- [Проекты Chromium](https://www.chromium.org/updates/first-party-sets).
