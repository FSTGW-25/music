<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Music Player</title>
    <link rel="stylesheet" href="style.css">
    <script src="script.js" defer></script>
    <link rel="shortcut icon" href="images/favicon.ico" type="image/x-icon">  
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.13.0/css/all.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</head>
<body>
   <div class="player">
       <div class="wrapper">
           <div class="details">
               <div class="now-playing">PLAYING x OF y</div>
               <div class="track-art"></div>
               <div class="track-name">Track Name</div>
               <div class="track-artist">Track Artist</div>
           </div>

           <div class="slider_container">
               <div class="current-time">00:00</div>
                <input type="range" min="1" max="100" value="0" class="seek_slider" onchange="seekTo()">
                <div class="total-duration">00:00</div>
           </div>

           <div class="slider_container">
               <i class="fa fa-volume-down"></i>
                <input type="range" min="1" max="100" value="99" class="volume_slider" onchange="setVolume()">
                <i class="fa fa-volume-up"></i>
           </div>

           <div class="buttons">
               <div class="random-track" onclick="randomTrack()">
                   <i class="fas fa-random fa-2x" title="random"></i>
               </div>
               <div class="prev-track" onclick="prevTrack()">
                    <i class="fa fa-step-backward fa-2x"></i>
                </div>
                <div class="playpause-track" onclick="playpauseTrack()">
                    <i class="fa fa-play-circle fa-5x"></i>
                </div>
                <div class="next-track" onclick="nextTrack()">
                    <i class="fa fa-step-forward fa-2x"></i>
                </div>
                <div class="repeat-track" onclick="repeatTrack()">
                    <i class="fa fa-repeat fa-2x" title="repeat"></i>
                </div>
           </div>

            <div id="wave">
                <span class="stroke"></span>
                <span class="stroke"></span>
                <span class="stroke"></span>
                <span class="stroke"></span>
                <span class="stroke"></span>
                <span class="stroke"></span>
                <span class="stroke"></span>
            </div>  
       </div>
   </div>
</body>
</html>




let now_playing = document.querySelector('.now-playing');
let track_art = document.querySelector('.track-art');
let track_name = document.querySelector('.track-name');
let track_artist = document.querySelector('.track-artist');

let playpause_btn = document.querySelector('.playpause-track');
let next_btn = document.querySelector('.next-track');
let prev_btn = document.querySelector('.prev-track');

let seek_slider = document.querySelector('.seek_slider');
let volume_slider = document.querySelector('.volume_slider');
let curr_time = document.querySelector('.current-time');
let total_duration = document.querySelector('.total-duration');
let wave = document.getElementById('wave');
let randomIcon = document.querySelector('.fa-random');
let curr_track = document.createElement('audio');

let track_index = 0;
let isPlaying = false;
let isRandom = false;
let updateTimer;

const music_list = [
    {
        img : 'foto 1.jpeg',
        name : 'SWEET THING',
        artist : 'SEVENTEEN',
        music : 'SWEETEST THING.mp3'
    },
    {
        img : 'foto 2.jpeg',
        name : 'FALLIN FLOWER',
        artist : 'SEVENTEEN',
        music : 'FALLIN FLOWER.mp3'
    },
    {
        img : 'foto 3.jpeg',
        name : 'CAMPFIRE',
        artist : 'SEVENTEEN',
        music : 'Seventeen-Campfire.mp3'
    },
    {
        img : 'foto 4.jpeg',
        name : 'SNAP SHOOT',
        artist : 'SEVENTEEN',
        music : 'Seventeen-Snap Shoot.mp3'
    }
];

loadTrack(track_index);
images
function loadTrack(track_index){
    clearInterval(updateTimer);
    reset();

    curr_track.src = music_list[track_index].music;
    curr_track.load();

    track_art.style.backgroundImage = "url(" + music_list[track_index].img + ")";
    track_name.textContent = music_list[track_index].name;
    track_artist.textContent = music_list[track_index].artist;
    now_playing.textContent = "Playing music " + (track_index + 1) + " of " + music_list.length;

    updateTimer = setInterval(setUpdate, 1000);

    curr_track.addEventListener('ended', nextTrack);
    random_bg_color();
}

function random_bg_color(){
    let hex = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e'];
    let a;

    function populate(a){
        for(let i=0; i<6; i++){
            let x = Math.round(Math.random() * 14);
            let y = hex[x];
            a += y;
        }
        return a;
    }
    let Color1 = populate('#');
    let Color2 = populate('#');
    var angle = 'to right';

    let gradient = 'linear-gradient(' + angle + ',' + Color1 + ', ' + Color2 + ")";
    document.body.style.background = gradient;
}
function reset(){
    curr_time.textContent = "00:00";
    total_duration.textContent = "00:00";
    seek_slider.value = 0;
}
function randomTrack(){
    isRandom ? pauseRandom() : playRandom();
}
function playRandom(){
    isRandom = true;
    randomIcon.classList.add('randomActive');
}
function pauseRandom(){
    isRandom = false;
    randomIcon.classList.remove('randomActive');
}
function repeatTrack(){
    let current_index = track_index;
    loadTrack(current_index);
    playTrack();
}
function playpauseTrack(){
    isPlaying ? pauseTrack() : playTrack();
}
function playTrack(){
    curr_track.play();
    isPlaying = true;
    track_art.classList.add('rotate');
    wave.classList.add('loader');
    playpause_btn.innerHTML = '<i class="fa fa-pause-circle fa-5x"></i>';
}
function pauseTrack(){
    curr_track.pause();
    isPlaying = false;
    track_art.classList.remove('rotate');
    wave.classList.remove('loader');
    playpause_btn.innerHTML = '<i class="fa fa-play-circle fa-5x"></i>';
}
function nextTrack(){
    if(track_index < music_list.length - 1 && isRandom === false){
        track_index += 1;
    }else if(track_index < music_list.length - 1 && isRandom === true){
        let random_index = Number.parseInt(Math.random() * music_list.length);
        track_index = random_index;
    }else{
        track_index = 0;
    }
    loadTrack(track_index);
    playTrack();
}
function prevTrack(){
    if(track_index > 0){
        track_index -= 1;
    }else{
        track_index = music_list.length -1;
    }
    loadTrack(track_index);
    playTrack();
}
function seekTo(){
    let seekto = curr_track.duration * (seek_slider.value / 100);
    curr_track.currentTime = seekto;
}
function setVolume(){
    curr_track.volume = volume_slider.value / 100;
}
function setUpdate(){
    let seekPosition = 0;
    if(!isNaN(curr_track.duration)){
        seekPosition = curr_track.currentTime * (100 / curr_track.duration);
        seek_slider.value = seekPosition;

        let currentMinutes = Math.floor(curr_track.currentTime / 60);
        let currentSeconds = Math.floor(curr_track.currentTime - currentMinutes * 60);
        let durationMinutes = Math.floor(curr_track.duration / 60);
        let durationSeconds = Math.floor(curr_track.duration - durationMinutes * 60);

        if(currentSeconds < 10) {currentSeconds = "0" + currentSeconds; }
        if(durationSeconds < 10) { durationSeconds = "0" + durationSeconds; }
        if(currentMinutes < 10) {currentMinutes = "0" + currentMinutes; }
        if(durationMinutes < 10) { durationMinutes = "0" + durationMinutes; }

        curr_time.textContent = currentMinutes + ":" + currentSeconds;
        total_duration.textContent = durationMinutes + ":" + durationSeconds;
    }
}




body {
    font-family: Arial, Helvetica, sans-serif;
    background: linear-gradient(to right,#9a1d86, #1595be);
    font-weight: bold;
    }
    
    .player{
      height: 95vh;
      display: flex;
      align-items: center;
      flex-direction: column;
      justify-content: center;
  }
    .wrapper{
      border: 1px solid transparent;
      padding: 30px;
      border-radius: 20px;
      background-color: #ddd;
      box-shadow: rgba(0, 0, 0, 0.3) 0px 19px 38px, rgba(0, 0, 0, 0.22) 0px 15px 12px;
    }
    
    .details {
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: center;
    }
    
    .track-art {
    margin: 25px;
    height: 250px;
    width: 250px;
    border:2px solid 	#FFFAFA;
    background-size: cover;
    background-position: center;
    border-radius: 50%;
    -moz-box-shadow: 0px 6px 5px black;
    -webkit-box-shadow: 0px 6px 5px black;
    box-shadow: 0px 6px 5px black;
    -moz-border-radius:190px;
    -webkit-border-radius:190px;
    border-radius:190px;
    }
    
    .now-playing {
    font-size: 1rem;
    }
    
    .track-name {
    font-size: 2.5rem;
    }
    
    .track-artist {
    margin-top: 5px;
    font-size: 1.5rem;
    }
    
    .buttons {
    display: flex;
    flex-direction: row;
    align-items: center;
    margin-bottom: 30px;
    }
    .active{
        color: black;
    }
    
    .repeat-track,
    .random-track,
    .playpause-track,
    .prev-track,
    .next-track {
    padding: 25px;
    opacity: 0.8;
    transition: opacity .2s;
    }
    
    .repeat-track:hover,
    .random-track:hover,
    .playpause-track:hover,
    .prev-track:hover,
    .next-track:hover {
    opacity: 1.0;
    }
    
    .slider_container {
    display: flex;
    justify-content: center;
    align-items: center;
    }
    
    .seek_slider, .volume_slider {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    height: 5px;
    background: #83A9FF;
    -webkit-transition: .2s;
    transition: opacity .2s;
    }
    
    .seek_slider::-webkit-slider-thumb,
    .volume_slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    width: 15px;
    height: 15px;
    background: white;
    border: 3px solid #3774FF;
    cursor: grab;
    border-radius: 100%;
    }
    
    .seek_slider:hover,
    .volume_slider:hover {
    opacity: 1.0;
    }
    
    .seek_slider {
    width: 60%;
    }
    
    .volume_slider {
    width: 30%;
    }
    
    .current-time,
    .total-duration {
    padding: 10px;
    }
    
    i.fa-volume-down,
    i.fa-volume-up {
    padding: 10px;
    }
    
    i,
    i.fa-play-circle,
    i.fa-pause-circle,
    i.fa-step-forward,
    i.fa-step-backward,
    p {
    cursor: pointer;
    }
    .randomActive{
        color: black;
    }
    .rotate {
        animation: rotation 8s infinite linear;
    }
    @keyframes rotation {
        from {
          transform: rotate(0deg);
        }
        to {
          transform: rotate(359deg);
        }
    }
    .loader {
      height: 70px;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .loader .stroke{
        background: #f1f1f1;
        height: 150%;
        width: 10px;
        border-radius: 50px;
        margin: 0 5px;
        animation: animate 1.4s linear infinite;
    }
      @keyframes animate {
        50% {
          height: 20%;
          background: #4286f4;
        }
    
        100% {
          background: #4286f4;
          height: 100%;
        }
      }
      .stroke:nth-child(1){
          animation-delay: 0s;
      }
      .stroke:nth-child(2){
        animation-delay: 0.3s;
    }
    .stroke:nth-child(3){
        animation-delay: 0.6s;
    }
    .stroke:nth-child(4){
        animation-delay: 0.9s;
    }
    .stroke:nth-child(5){
        animation-delay: 0.6s;
    }
    .stroke:nth-child(6){
        animation-delay: 0.3s;
    }
    .stroke:nth-child(7){
        animation-delay: 0s;
    }
    
