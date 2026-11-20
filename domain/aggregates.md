# Агрегаты SkillSwap

## 1. `Listing` — публикация
**Корень:** `Listing`. **Назначение:** предложение об обучении или запрос на освоение навыка.
**Состав:** `ListingId`, `OwnerId`, `SkillId`, `ListingType` (`OFFER`/`REQUEST`), `Description`, `ListingStatus`, `CreatedAt`, `UpdatedAt`.
**Инварианты:** владелец существует; навык выбран; описание 20–1000 символов; тип только OFFER/REQUEST; закрытая публикация не принимает новые обмены; изменять может только владелец.
**Команды:** `CreateListing`, `PublishListing`, `UpdateListing`, `CloseListing`. **События:** `ListingCreated`, `ListingPublished`, `ListingUpdated`, `ListingClosed`. **Связь:** `FR-20`, `FR-21`.

## 2. `Exchange` — обмен
**Корень:** `Exchange`. **Состав:** `ExchangeId`, `ListingId`, `InitiatorId`, `RecipientId`, `OfferedSkillId`, `RequestedSkillId`, `ExchangeStatus`, `TermsVersion`, `Session`, `CompletionConfirmations`, timestamps.
**Статусы:** `PENDING`, `CHANGE_PROPOSED`, `ACCEPTED`, `SCHEDULED`, `AWAITING_CONFIRMATION`, `COMPLETED`, `REJECTED`, `CANCELLED`.
**Инварианты:** инициатор и получатель различны; обмен создаётся только по активной публикации; ответ возможен только при PENDING; изменение условий повышает `TermsVersion` и требует повторного согласования; занятие назначается после принятия; подтверждать могут только участники; `COMPLETED` устанавливается только при двух подтверждениях одной версии занятия; отклонённый/отменённый обмен завершить нельзя.
**Команды:** `ProposeExchange`, `AcceptExchange`, `RejectExchange`, `ProposeTermsChange`, `AgreeTerms`, `ScheduleSession`, `MarkSessionHeld`, `ConfirmCompletion`, `CancelExchange`.
**События:** `ExchangeProposed`, `ExchangeAccepted`, `ExchangeRejected`, `ExchangeTermsAgreed`, `SessionScheduled`, `SessionMarkedAsHeld`, `CompletionConfirmed`, `ExchangeCompleted`, `ExchangeCancelled`. **Связь:** `FR-22`, `FR-23`.

## 3. `Review` — отзыв
**Корень:** `Review`. **Состав:** `ReviewId`, `ExchangeId`, `AuthorId`, `RecipientId`, `Rating`, `Comment`, `CreatedAt`.
**Инварианты:** обмен COMPLETED; автор и получатель — разные участники; один отзыв автора на обмен; оценка 1–5; рейтинг рассчитывается по последним 50 отзывам, старые сохраняются.
**Команды:** `CreateReview`, `GetRecentRating`. **События:** `ReviewPublished`, `ParticipantRatingUpdated`. **Связь:** `FR-24`.

## Примечание к модели
`USERS` и `SKILLS` — справочные сущности; `USER_SKILLS`, `SESSIONS` и `EXCHANGE_CONFIRMATIONS` — связи/составные данные. Завершение обмена должно обрабатываться транзакционно внутри агрегата `Exchange`.
