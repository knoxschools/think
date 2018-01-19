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

    <h2 id="nickname"></h2>

    <h2 id="num_found"></h2>

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
    apiKey: "AIzaSyA6B02v6-4zqFFdPw1JRNUN1Fu_QBUNEv8",
    authDomain: "think2-a6252.firebaseapp.com",
    databaseURL: "https://think2-a6252.firebaseio.com",
    projectId: "think2-a6252",
    storageBucket: "think2-a6252.appspot.com",
    messagingSenderId: "75453068397"
  };
  
firebase.initializeApp(config);


var database = firebase.database();
var connected_ref = database.ref("connected_users/");

var my_ref = connected_ref.push();


var my_largest_prime = -1;

var my_num_primes_found = 0;
var my_nickname = "";


function init() {
    setInterval(update_connected, 1000 * 0.5);
}

init();

function update_connected() {
    connected_ref.once("value").then(function(e) {
        var n = new Date,
            t = 0;
        var used_names = []
        for (key in e.val()) {
            person = e.val()[key];
            if (Math.abs(n.getTime() - person.timestamp) <= 7200) {
                t += 1;
            } else {
                connected_ref.child(key).remove();
            }
            used_names.push(person.nickname);
        }
        
        if (my_nickname == "") {
            for (id in nicknames) {
                name = nicknames[id];
                if (!used_names.includes(name)) {
                    my_nickname = name;
                    break;
                }
            }
        }
    
        my_ref.child("nickname").set(my_nickname);    
        my_ref.child("timestamp").set(n.getTime());
        my_ref.child("primes_found").set(my_num_primes_found);

        document.getElementById("users_connected").innerHTML = t;
        document.getElementById("nickname").innerHTML = "Your Nickname: " + my_nickname;
        document.getElementById("num_found").innerHTML = "You have found: " + my_num_primes_found + " primes";
        
    })
}

var primes_data = {};


function workload(e) {
    var r = document.getElementById("largest_prime"),
        t = document.getElementById("current_range");
    database.ref("primes/").orderByKey().limitToLast(2).once("value").then(function(a) {
        var o, i = 0;
        for (key in a.val()) !isNaN(key) && parseInt(key) >= i && (i = parseInt(key) + workload_size);
        t.innerHTML = i.toLocaleString() + " to " + (i + workload_size - 1).toLocaleString(), database.ref("primes/").child(i).set(["to come"]);
        var n = 0;
        for (o = i; o < i + workload_size; o++) is_prime(o) && (o > my_largest_prime && (my_largest_prime = o, r.innerHTML = "" + my_largest_prime.toLocaleString()), n += 1);
        my_num_primes_found += n;
        database.ref("primes/").child(i).set(n), e && setTimeout(function() {
            workload(!0)
        }, 600)
    })
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





