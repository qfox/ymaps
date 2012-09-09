ymaps
=====

ymaps related things

Examples: http://zxqfox.github.com/ymaps/examples/

Car
---

Simple javascript object for animating some route on yandex.maps (http://api.yandex.ru/maps/doc/jsapi/2.x/ref/)

Examples: http://zxqfox.github.com/ymaps/examples/car/

### Simple example usage
```js
$.getScript('car.js', function () {
  car = new Car();
  ymaps.route([
    'Москва, метро Смоленская', // from Smolenskaya subway station
    [55.740532, 37.625693] // to Tretyakovkaya subway station
  ]).then(function (route) {
    car.moveTo(route.getWayPoints().getPaths().get(0).getSegments());
  });
});
```

### More complex
```js
$.getScript('car.js', function () {
  car = new Car({
    // geoObject options
    iconLayout: ymaps.templateLayoutFactory.createClass(
      '<div class="b-car b-car_blue b-car-direction-$[properties.direction]"></div>'
    )
  });
  ymaps.route([
    'Москва, метро Смоленская', // from Smolenskaya subway station
    {
      type: 'viaPoint', // via Tretyakovskaya gallery
      point: 'Россия, Москва, Лаврушинский переулок, 10'
    },
    [55.740532, 37.625693] // to Tretyakovkaya subway station
  ], {
    // Router options
    mapStateAutoApply: true
  }).then(function (route) {
    // Setting custom label for routing points
    var points = route.getWayPoints();
    points.get(0).properties.set("iconContent", "A");
    points.get(1).properties.set("iconContent", "B");

    // Add route and car to map
    map.geoObjects.add(route);
    map.geoObjects.add(car);

    // Send car with specific move speed and directions count
    car.moveTo(route.getPaths().get(0).getSegments(), {
      speed: 10,
      directions: 8
    }, function (geoObject, coords, direction) { // тик движения
      // перемещаем машинку
      geoObject.geometry.setCoordinates(coords);
      // ставим машинке правильное направление - в данном случае меняем ей текст
      geoObject.properties.set('direction', direction.t);

    }, function (geoObject) { // приехали
      geoObject.properties.set('balloonContent', "Приехали!");
      geoObject.balloon.open();
    });
  });
});
```

Published under MIT License