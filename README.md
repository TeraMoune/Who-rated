# DLE-Who-rated
Плагин ведёт учёт даты изменения рейтинга у новостей и комментариев. Добавляет функцию по вызову которой осуществляет AJAX запрос и возвращает окно с участниками кто влиял на рейтинг, по нажатию на имя пользователя покажет его историю оценок и их дату.

## Возможности:
* Статистика голосующих отсортированная по дате в отдельном окне с разбивкой по страницам.
* Отдельная статистика последних 100 голосований пользователя.
* Поддержка рейтинга комментариев.

## Дополнительная информация

<details>
	<summary>CSS code</summary>
  
```css
/** Who rated module **/
.who-rated {
	width: 500px;
    margin: 0 auto;
    position: relative;
	background: #FFF;
    padding: 25px 10px 10px;
    border-radius: 3px;
	border: 1px solid #ccc;
}
#who-rated ul {
	list-style-type: none;
    margin: 0;
    padding: 0;	
}

#who-rated li.ratingLog-liminus .rated-is {
	color: #ba3636;
}

#who-rated li.ratingLog-liplus .rated-is {
	color: #3caa00;
}

#who-rated li .rated-is .icon {
    width: 16px;
    height: 16px;
    fill: currentColor;	
}

#who-rated li .rated-is {
	position: absolute;
	left: 0px;
	top: 4px;
	width: 22px;
	height: 22px;
	line-height: 22px;
	text-align: center;
}

#who-rated li.ratingLog-listars .rated-is {
	font-weight:700;
	top: 5px !important;
}

#who-rated span.rated-is-plus {
	color: #3caa00;
	top: -2px;
	position: relative;
}

#who-rated span.rated-is-minus {
	color: #ba3636;
	top: -2px;
	position: relative;
}

#who-rated li {
	font: normal 11px/22px Verdana;
	cursor: pointer;
	border-radius: 2px;
	position: relative;
}

#rated-list li {padding: 4px 0px 4px 53px;}
#rate-profile-stats li{padding: 4px 0px 4px 23px;}

#who-rated ul li:hover {
	color: #000;
	background-color: #f5f5f8;
}

#who-rated li a {
	display: inline-block;
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
	vertical-align: middle;
	width: 75%;	
}

#who-rated li .rated-date {
	position: absolute;
	right: 6px;
	top: 4px;
	font: normal 10px/22px Tahoma;
	color: #999;
}

#who-rated li img {
	position: absolute;
	left: 24px;
	top: 4px;
	height: 22px;
	width: 22px;
	border-radius: 1px;
}

#rated-list-nav {width: 100px;margin: 0 auto;}
#rated-list-nav li {font-size: 20px;font-weight: 700;cursor: pointer;}
#rated-list-nav li.disabled {color:#AAA;}
#rated-list-nav li:nth-child(1) {float:left}
#rated-list-nav li:nth-child(2) {float:right;}

#who-rated .ratingLog-userbox-user {
	height: 60px;
	padding: 10px 50px 10px 5px;
	border-bottom: 1px dotted #ddd;
	font: normal 16px/25px 'Roboto Condensed', sans-serif;
	position: relative;
	box-sizing: content-box;
}

#who-rated .ratingLog-userbox-user div.stars-block {
	display:inline-block;
	margin-left: 10px;
}

#who-rated .ratingLog-userbox-user div.stars-block:first-child {
	margin-left: 0px;
}

#who-rated .ratingLog-userbox-user img {
	height: 60px;
	width: 60px;
	margin: 0 10px 0 0;
	float: left;
	border-radius: 3px;
}

#who-rated .ratingLog-userbox-close {
	position: absolute;
	right: 12px;
	top: 50%;
	margin-top: -10px;
	width: 19px;
	height: 19px;
	opacity: .7;
	cursor: pointer;
}

#who-rated .ratingLog-userbox ul {
	max-height: 300px;
	overflow: auto;
}

#who-rated .rate-profile-stats .no_row {
	padding: 10px;
	text-align: center;
	display: inline-block;
	width: 100%;
}
/** Who rated END **/
```
</details>

<details>
  <summary>JavaScript code</summary>
 
```javascript
function profileRateStats(id, rating_type) {
	
	//ShowLoading();	
	$.post( dle_root + "engine/ajax/controller.php?mod=who_rated", {user_id: id, type:'profile', rating_type: rating_type, user_hash: dle_login_hash}, function(data) {
		//HideLoading();
		$('#who-rated .all-list').hide();	
		$('#who-rated').append(data);
    //Тут инициализация плагинов если требуется.
		/*new LazyLoad({
			elements_selector: "#rate-profile-stats img[data-src]",
			threshold: 0,
			load_delay: 250
		});*/
		
	});	
					
}

function who_rated_list(obj, rating_type) {
	
	var id = $(obj).data('commid') ? $(obj).data('commid') : $(obj).data('newsid');
	//ShowLoading();
	$.post( dle_root + 'engine/ajax/controller.php?mod=who_rated', {id: id, rating_type: rating_type, user_hash: dle_login_hash}, function(data){
		//HideLoading();
		if( data == 'null-1' ) {
			
			//Box.InfoNormal('who-rated', 'Информация', 'Новость не имеет голосов', 400, 2000);
			DLEalert('Новость не имеет голосов', 'Информация');
      
		} else {
			
			$.magnificPopup.open({
				items: {
					src: '<div class="who-rated clrfix">'+data+'</div>'
				},
				type: 'inline',
				mainClass: 'mfp-fade',
				removalDelay: 0,
				overflowY: 'hide',
				closeOnBgClick: true,
				callbacks: {
					open: function() {
            //Тут инициализация плагинов если требуется.
						/*new LazyLoad({
							elements_selector: "#rated-list img[data-src]",
							threshold: 0,
							load_delay: 250
						});*/
					},
					afterClose: function() {},		 
					beforeClose: function() {}
				}		
				});
		}
	});
			
	return false;
	
}
```
</details>

> Функция `who_rated_list` имеет два параметра. Первый должен быть `this` или любой другой объект на котором будет находится нужные атрибуты `data-commid` или `data-newsid`. Второй параметр указывается в случае запроса для получения рейтинга комментария. Если для новости то параметр не указывать.

> Для рейтинга `нравится \ не нравится` устанавливать кнопки не нужно.

> Для рейтинга типа "Звёзды" установка кнопки происходит отлельно.

Пример для шаблона комментария:
```html
		[rating]
		<div class="rate">
			[rating-type-1]<div class="rate_stars"><span data-commid="{id}" onclick="who_rated_list(this, 1);">[?]</span>{rating}</div>[/rating-type-1]
		</div>
		[/rating]      
```
