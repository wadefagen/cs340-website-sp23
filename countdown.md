---
layout: none
---
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="{{ site.description }}">
    <meta name="author" content="">
    <meta http-equiv="Cache-control" content="no-cache, must-revalidate">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
    <link href="https://fonts.googleapis.com/css2?family=Alata&family=Montserrat:wght@400;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="{{ site.baseurl }}/static/css/cs240.css">
    <link rel="icon"  type="image/png" href="{{ site.baseurl }}/static/favicon.png">

  </head>
  <body>
    <style>
    .clock {
      font-size: 120px;
      font-family: Montserrat;
      font-weight: bold;
      color: #E84A27;
    }
    .screen-center {
      display: table;
      width: 100%;
      height: 100%;
    }
    .screen-center > div {
      display: table-cell;
      vertical-align: middle;
    }
    </style>
    <div class="screen-center">
      <div class="container text-center">
        <div class="clock">
          0:00
        </div>
        <h3>CS 340: Introduction to Computer Systems</h3>
      </div>
    </div>    
  </body>
  <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js" integrity="sha384-wHAiFfRlMFy6i5SRaxvfOCifBUQy1xHdJ/yoi7FRNXMRBu5WHdZYu1hA6ZOblgut" crossorigin="anonymous"></script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js" integrity="sha384-B0UglyR+jN6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous"></script>
  <script>
  var target = new Date("2020-09-24 12:30:30").getTime();
  function update() {
    let now = new Date().getTime();
    let distance = target - now;
    let minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
    let seconds = Math.floor((distance % (1000 * 60)) / 1000);
    minutes = minutes.toString();
    seconds = seconds.toString();

    let str = "";

    if (minutes >= 0 && seconds >= 0) {
      str = minutes + ":" + seconds.padStart(2, "0");
      setTimeout(update, 1000);
      console.log(setTimeout);
    } else {
      str = "Soon!";
    }

    $(".clock").html(str);
  }
  $(function () {  
    update();
  });
  </script>
</html>
