# XML

XML (eXtensible Markup Language) - расширяемый язык разметки. Рекомендован Консорциумом Всемирной паутины (W3C). XML разрабатывался как язык с простым формальным синтаксисом, удобный для создания и обработки документов как программами, так и человеком, с акцентом на использование в Интернете. Язык называется расширяемым, поскольку он не фиксирует разметку, используемую в документах: разработчик волен создать разметку в соответствии с потребностями к конкретной области, будучи ограниченным лишь синтаксическими правилами языка.

Расширение XML — это конкретная грамматика, созданная на базе XML и представленная словарём тегов и их атрибутов, а также набором правил, определяющих, какие атрибуты и элементы могут входить в состав других элементов. Сочетание простого формального синтаксиса, удобства для человека, расширяемости, а также базирование на кодировках Юникод для представления содержания документов привело к широкому использованию как, собственно, XML, так и множества производных специализированных языков на базе XML в самых разнообразных программных средствах.

Для корректного XML документа должны исполняться следующие условия:

- Правильное оформление документа.
- Соблюдаться все синтаксические правила XML.
- Документ должен соответствовать семантическим правилам языка (которые обычно заданны в схеме XML или DTD (Document Type Definition)).

```xml
<?xml version="1.0"?>
<PurchaseOrder PurchaseOrderNumber="99503" OrderDate="1999-10-20">
	<Address Type="Shipping">
		<Name>Ellen Adams</Name>
		<Street>123 Maple Street</Street>
		<City>Mill Valley</City>
		<State>CA</State>
		<Zip>10999</Zip>
		<Country>USA</Country>
	</Address>
	<Address Type="Billing">
		<Name>Tai Yee</Name>
		<Street>8 Oak Avenue</Street>
		<City>Old Town</City>
		<State>PA</State>
		<Zip>95819</Zip>
		<Country>USA</Country>
	</Address>
	<DeliveryNotes>Please leave packages in shed by driveway.</DeliveryNotes>
	<Items>
		<Item PartNumber="872-AA">
			<ProductName>Lawnmower</ProductName>
			<Quantity>1</Quantity>
			<USPrice>148.95</USPrice>
			<Comment>Confirm this is electric</Comment>
		</Item>
		<Item PartNumber="926-AA">
			<ProductName>Baby Monitor</ProductName>
			<Quantity>2</Quantity>
			<USPrice>39.98</USPrice>
			<ShipDate>1999-05-21</ShipDate>
		</Item>
	</Items>
</PurchaseOrder>
```
- Пример XML файла:
