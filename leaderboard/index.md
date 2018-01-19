---
layout: nothing
current: leaderboard
title: Leaderboard
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

    <h2>
    Users Connected:
    </h2>
    <h1 id="users_connected">
    0
    </h1>

    <h2 id="place_1">
    </h2>

    <h2 id="place_2">
    </h2>

    <h2 id="place_3">
    </h2>


    <h2 id="place_4">
    </h2>

    <h2 id="place_5">
    </h2>


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


function update_connected() {
    connected_ref.once("value").then(function(e) {
        var n = new Date, t = 0;
        var people = [];
        for (key in e.val()) {
            person = e.val()[key];
            people.push(person);
            if (Math.abs(n.getTime() - person.timestamp) <= 7200) {
                t += 1;
            } else {
                connected_ref.child(key).remove();
            }
        }

        
        
        function sorter(a, b) {
            if (a.primes_found < b.primes_found) {
                return -1;
            } else if (a.primes_found == b.primes_found) {
                return 0;
            } else {
                return 1;
            }
        }
    
        document.getElementById("users_connected").innerHTML = "" + people.length;
    
        var sorted_people = people.sort(sorter);
        console.log(sorted_people);
        
        function do_thing(n) {
            if (sorted_people.length >= n) {
                document.getElementById("place_" + n).innerHTML = n + ". " + sorted_people[sorted_people.length - n].nickname + " has found " + sorted_people[sorted_people.length - n].primes_found + " primes";
            } else {
                document.getElementById("place_" + n).innerHTML = "";
            }
        }
        
        do_thing(1);
        do_thing(2);
        do_thing(3);
        do_thing(4);
        do_thing(5);
        

    })
}
var primes_data = {};


init();


</script>





