# MFA

# Что такое многофакторная аутентификация?

💡 Многофакторная аутентификация (MFA) - это безопасный процесс аутентификации, который требует использования более одной техники аутентификации, выбираемых из независимых категорий идентификационных данных.

Многофакторную аутентификацию также иногда называют «двухфакторной аутентификацией (2FA)».

В проверке подлинности того, за кого вы себя выдаете, участвуют два фактора. Первым фактором является пароль, который создается при создании учетной записи пользователя.

Вторым фактором будет любое приложение, генерирующее OTP, или любой протокол, отправляющий вам текстовые сообщения или совершающий звонок на ваше устройство.

В зависимости от того, как реализовано приложение, способы аутентификации различаются. Некоторые общие инструменты, используемые для MFA, следующие:

- Приложение, установленное на мобильном устройстве, которое генерирует токены.
- Внешнее устройство, например Yubikey.
- Отпечаток пальца.
- Распознавание лица.
- OTP-пароль на основе текстового сообщения или звонка.
- Для включения многофакторной аутентификации для ssh мы будем использовать приложение «Google Authenticator», которое использует протокол OATH-TOTP. Существуют и другие альтернативные инструменты, такие как Twilio Authy или FreeOTP, которые вы можете установить и опробовать.

