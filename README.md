<html>
	<head>Coder Dojo Demo</head>
	<style>
		body {  
			font-size: 20px;
			max-width: 900px;
		}
		
		pre {
			background-color: rgba(0,0,0,1);
			color: #fff;
			padding: 20px;
			word-wrap: break-word;
		}
	</style>
	<body>
		<ol>
			<li>Anyone can do HTML5 Audio.
				<ul>
					<li>
						Put this in your console
<pre>
var msg = new SpeechSynthesisUtterance("Oh, so they have the Internet on computers now!");			
speechSynthesis.speak(msg);
</pre>		
					</li>
					<li>
						You can also change the voice, the pitch, speed and culture.
<pre>
var msg = new SpeechSynthesisUtterance("This perpetual motion machine Lisa made today is a joke. It just keeps going faster and faster");			
msg.voice = speechSynthesis.getVoices().filter(function(voice) { return voice.name == 'Zosia'; })[0];
msg.rate = 1
msg.pitch = 0.3
speechSynthesis.speak(msg);
</pre>		
					</li>
					<li>
						Most importantly you can make noise
<pre>
(function(){
	var context = new AudioContext();
	    osc = context.createOscillator(),
	    osc2 = context.createOscillator(),
	    gain = context.createGain(),
	    w = window.innerWidth,
	    h = window.innerHeight;

    osc.frequency = 400;
	osc.type = 'sawtooth';
    osc.connect(context.destination);
    osc.start(0);
		
    gain.gain.value = 100;
    gain.connect(osc.frequency);

    osc2.frequency.value = 1;
	osc2.type = 'square';
    osc2.connect(gain);
    osc2.start(0);

    document.addEventListener("mousemove", function(e) {
        osc.frequency.value = e.clientY / h * 1000 + 200;
        osc2.frequency.value = e.clientX / w * 30 + 1;
    });
})();
</pre>		
					</li>
				</ul>
			</li>
			<li>Doppler Pong!<br /> Lets take this <a href="https://github.com/dasmikko/dasmikko.github.io/tree/master/public/games/pong-game" target="_blank">pong</a></br> game and this bit of <a target="_blank" href="http://danielrapp.github.io/doppler/">'doppler'</a> code, mash it together and see what we get.</a></li>
			<li><ul>
				<li>
					Start a simple webserver
<pre>
python -m SimpleHTTPServer
</pre>
				</li>
				<li>Get Doppler Bandwidth on load
					<pre>
window.addEventListener('load', function() {
	window.doppler.init(function(bandwidth) {
		var threshold = 4;
		if (bandwidth.left > threshold || bandwidth.right > threshold) {
			window.dopplerBandwidth = bandwidth;
		}
	});
});
					</pre>
				</li>
				<li>
					Add some properties we will need to the game class
					<pre>
var dopplerEasingUpCount;
var dopplerEasingDownCount;
var dopplerPaddleDirection;
					</pre>
				</li>
				<li>And give those properties values on game start and function resetBall
					<pre>
this.dopplerEasingUpCount = 100;
this.dopplerEasingDownCount = 100;
this.dopplerPaddleDirection = 'up'
					</pre>
				</li>
				<li>We are only interested in moving the right paddle with our motion so lets make the left paddle computer controlled in the update function.
					<pre>
this.paddle1.body.y = this.ball.y;
					</pre>
				</li>
				<li>Still in the update function add up and down motion based on the doppler bandwith
					<pre>
if(window.dopplerBandwidth.left > window.dopplerBandwidth.right)
{
	this.paddle2.body.y -= window.dopplerBandwidth.left * 1.5;
}
else
{
	this.paddle2.body.y += window.dopplerBandwidth.right;
}
					</pre>
				</li>
				<li>Err once the paddle starts in the one direction it does not stop even when our hand stops. Lets add some fake easing that makes the paddle slow down to a stop.
					<pre>
console.log(window.dopplerBandwidth);
if(window.dopplerBandwidth.left > window.dopplerBandwidth.right)
{
	//console.log('up')
	if(this.dopplerPaddleDirection === 'down') {
		this.dopplerEasingDownCount = 100;
		this.dopplerPaddleDirection = 'up';
		console.log('change');
	}

	if(this.dopplerEasingUpCount > 0) {
		this.paddle2.body.y -= (window.dopplerBandwidth.left * 1.5) * (this.dopplerEasingUpCount / 100);
	}
	else {
		this.paddle2.body.y = this.paddle2.body.y;
	}

	this.dopplerEasingUpCount--;

	//console.log(this.dopplerEasingUpCount);
}
// move down with doppler
else if(window.dopplerBandwidth.right > window.dopplerBandwidth.left)
{
	//console.log('down')
	if(this.dopplerPaddleDirection === 'up') {
		this.dopplerEasingUpCount = 100;
		this.dopplerPaddleDirection = 'down';
	}

	if(this.dopplerEasingDownCount > 0) {
		this.paddle2.body.y += (window.dopplerBandwidth.right * 1.5) * (this.dopplerEasingDownCount / 100);
	}
	else {
		this.paddle2.body.y = this.paddle2.body.y;
	}

	this.dopplerEasingDownCount--;
}
					</pre>
				</li>
			</ul></li>
		</ol>
				
	</body>
</html>