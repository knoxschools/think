---
layout: nothing
current: go
title: go
navigation: true
class: page-template
subclass: 'post page'
---


<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
<script src="https://www.gstatic.com/firebasejs/4.8.2/firebase.js"></script>

{% include think_workloaddef.html %}


<style>

@font-face {
  font-family: 'UbuntuMono';
  src: url('{{site.myurl}}assets/UbuntuMono.ttf')  format('truetype'), /* Safari, Android, iOS */
}

body {
    background-color: #888;
    font-family: 'UbuntuMono', Fallback, sans-serif;
    margin: 3em;
    
}

.mymobile {
    padding-bottom: 2em;
}

@media screen and (min-width: 320px) {
    .mymobile {
        width: 100%;
    }

    h1 {
        font-size: 4.0em;
    }
    h2 {
        font-size: 3.4em;
    }

}

@media screen and (min-width: 1200px) {
    .mymobile {
        width: 32em;
    }


    h1 {
        font-size: 2.1em;
    }
    h2 {
        font-size: 1.8em;
    }
}

background-color

</style>


<div style="text-align: center;">
    <!--<h1>Th!nk</h1>-->
    <a href="{{site.myurl}}"><img class="mymobile" src="{{site.myurl}}assets/images/blog-icon.png"></a>

    <br/>

    <h2>
    Your Largest Prime:
    </h2>

    <h1 id="largest_prime">
    ...
    </h1>

    <br/>

    <h2>
    Testing Range:
    </h2>
    <h1 id="current_range">
    ...
    </h1>

    <br/>

    <h2>
    Users Connected:
    </h2>
    <h1 id="users_connected">
    0
    </h1>


</div>


<script>

var config = {
    apiKey: "AIzaSyBqTpWrqnXMAudyAWIQsA4tlyuoAvMYfHU",
    authDomain: "think-12e6f.firebaseapp.com",
    databaseURL: "https://think-12e6f.firebaseio.com",
    projectId: "think-12e6f",
    storageBucket: "think-12e6f.appspot.com",
    messagingSenderId: "17821661067"
};

firebase.initializeApp(config);


var database = firebase.database();
var connected_ref = database.ref("connected_users/");

var my_ref = connected_ref.push();


var my_largest_prime = -1;


function init() {
    setInterval(update_connected, 1000 * 1.0);
}

init();

function update_connected() {
    connected_ref.once('value').then(function (val) {
        var cdate = new Date();
        var ct = 0;
        for (key in val.val()) {
            person = val.val()[key];
            if (Math.abs(cdate.getTime() - person.timestamp) <= 7.2 * 1000) {
                ct += 1;
            } else {
                connected_ref.child(key).remove();
            }
        }
        my_ref.child("timestamp").set(cdate.getTime());

        document.getElementById("users_connected").innerHTML = ct;
    });
}

var primes_data = {};


function workload(recurse) {
    var maxprime = document.getElementById("largest_prime");   
    var curblock = document.getElementById("current_range");

    //database.ref('primes').once('value').then(function (val) {
    database.ref("primes/").orderByKey().limitToLast(2).once("value").then(function (val) {
        var prime = 0;
        for (key in val.val()) {
            if (!isNaN(key) && parseInt(key) >= prime) {
                prime = parseInt(key) + workload_size;
            }
        }

        curblock.innerHTML = prime.toLocaleString() + " to " + (prime + workload_size - 1).toLocaleString();

        database.ref('primes/').child(prime).set(["to come"]);

        var i;
        var res_ct = 0;

        for (i = prime; i < prime + workload_size; i++) {
            if (is_prime(i)) {
                if (i > my_largest_prime) {
                    my_largest_prime = i;
                    maxprime.innerHTML = "" + my_largest_prime.toLocaleString();
                }
                res_ct += 1;
            }
        }
        database.ref('primes/').child(prime).set(res_ct);

        if (recurse) {
            setTimeout(function() {workload(true)}, 600);
        }
    });
}


function is_prime(x) {
    var y;
    if (x < 2) return false;
    if (x == 2) return true;
    if (x % 2 == 0) return false;
    for (y = 3; y * y <= x; y += 2) {
        if (x % y == 0) {
            return false;
        }
    }
    return true;
}

// autostart
workload(true);

</script>





