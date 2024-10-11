# Party Acceleration

Party Acceleration is a 256 byte intro released at [Deadline 2024](https://www.demoparty.berlin/)

**You can [watch it online here](https://teadrinker.net/prods/party-acceleration/)**

## How it works

The main idea is just to reuse the sound function as data for the visuals, and doing so should also give some nice sync. A waveform display basically. The sound is a kick alternating with a 8th bass note, with a [fake filter](https://github.com/teadrinker/bytebeat-notes?tab=readme-ov-file#approximated-filtered-sawtooth). The time is warped ([like this](https://madtealab.com/#V=1&F=1&G=1&GX=40&GY=19&GS=0.045&f1=x+%3E+16+%3F+x+-+8+%2B+pow%282%2C+x-34%29+%3A+x+%2A+x+%2F+32)), initially the speed is increasing, then linear for a while, resembling normal music, and then finally the speed increases exponentially. The loss of float32 precision as the numbers get high creates some nice visual patterns at the end. However relying on this is a bit brittle, on my machine the sound stops when the visuals end, however on other systems the sound continue it seems...

I made 13 versions of this, the hardest part was trying to make the sound not too agressive as it's passing the ears sensitive range when pitching up.

## Interactive Programming

As usual when I prototype sound and visuals for size coding, I used Mad Tea Lab to iterate faster. 

[Play with a monochrome version of the intro in Mad Tea Lab](https://madtealab.com/#V=1&C=6&F=1&G=1&R=0&O=1&W=709&GW=655&GH=385&GX=160&GY=-120&GS=0.0076&EH=319&aMa=50&aS=1.53&aN=audioTime&bMa=50&bN=startAtTime&c=43&cMa=100&cI=1&cA=1&cS=0.79&cN=animToForceUpdate&d=3711&dMa=3711&dI=1&l=1408&lMa=1408&lI=1&m=6336&mMa=6336&mI=1&f1=clip%28snd%28%28%28x+%2B+startAtTime%29+%2A+88200%29%29%29&S=3&SLE=200&Expr=%0A%2F%2F+hacky+MicroW8+wrapper%2C+see+code+further+below%0A%0Adraw+%3D+%28%29+%3D%3E+%7B%0A%0A+var+latency+%3D+10500%2F44100++%2F%2F+hardcoded+latency+compensation+%28tweaked+on+windows%2Fchrome%29%0A%0A+if%28audioPos%28%29%29%0A++audioTime+%3D+max%280%2C+audioPos%28%29+-+latency%29+%0A%0A+var+t+%3D+audioTime+%2B+startAtTime%0A+plotSpace%28%29%0A+lin%280%2C0%2C+320%2C+0%29%0A+lin%280%2C0%2C++0%2C+240%29%0A+lin%280%2C240%2C+320%2C+240%29%0A+lin%28320%2C0%2C+320%2C+240%29%0A+upd%28t%29%0A%7D%0A%0Avar+fmod+%3D+%28x%2Ca%29+%3D%3E+x+%25+a+%0Avar+setPixel+%3D+%28x%2Cy%29+%3D%3E+%7B+if%28x%3E0%26%26x%3C320%26%26y%3E0%26%26y%3C240%29%7B+fill%28255%2C70%29%3B+point%28floor%28x%29%2Cfloor%28-y%29%29+%7D+%7D%0Avar+lin+%3D+%28x%2Cy%2Cx2%2Cy2%29+%3D%3E+%7B+line%28x%2C-y%2Cx2%2C-y2%29+%7D%0Avar+select+%3D+%28x%2Ca%2Cb%29+%3D%3E+x+%3F+a+%3A+b%0A%0A%0A%0A%0A%2F%2F+press+the+looping+play+button+on+the+functions+section%0A%0Aupd+%3D+%28t%29+%3D%3E+%7B%0A+++%0A+++var+it+%3D+t+%2A+88200%0A%0A+++for%28var+i+%3D+0%3B+i+%3C+12800%3B+i%2B%3D1%29+%7B%0A+++++let+s+%3D+t+%2B+%28%28i%2A512%29+%26+%28d%2A2+-+d%2Acos%28max%286%2Ct%2F2%29%29%29+%29%2F64+%0A+++++let+x+%3D+snd%28%28i%3E%3E1%29+%2B+it+%2B+l%29%2As%0A+++++let+y+%3D+snd%28%28i%3E%3E1%29+%2B+it+-+m+-+int%28t%2A256%29%29%2As+%0A+++++let+rx+%3D+x+-+y%0A+++++let+ry+%3D+y+%2B+x%0A+++++setPixel%28rx+%2B+160%2C+ry+%2B+120%29+++++%0A+++%7D+%0A%0A%7D%0A%0Asnd+%3D+%28sampleIndex+%29+%3D%3E+%7B%0A%0A++let+xx+%3D+sampleIndex+%2F+88200.0%3B%0A++let+warpedTime+%3D+select%28xx+%3E+16.0%2C+xx+-+8.0+%2B+pow%282.0%2C+xx-34.0%29%2C+xx+%2A+xx+%2F+32%29%3B++%0A++let+x+%3D+warpedTime%3B%0A++x+%3D+fmod%28x%2C+0.5%29%3B%0A++x+%3D+select%28x%3C0.25%2C+pow%28x%2C+0.08349609375%29%2C+x%29%3B+%2F%2F+kick+or+bass%0A++x+%3D+x+%2A+128.0%3B%0A++x+%3D+fmod%28x%2C+2.0%29+-+1.0%3B%0A%0A++%2F%2F+f%3D0+filtered%2C+f%3D1+unfiltered++%0A++let+f+%3D+%281.0+-+cos%28warpedTime%2F8.0%29%29+%2A+fmod%28warpedTime%2C+1.0%29%3B+%0A%0A++%2F%2F+apply+fake+filter+and+clip-distortion%0A++return+max%28-1.0%2C+min%281.0%2C+++%28x%2Ax%2Ax-x%29%2F%28f%2Ax%2Ax+-+1.0%29%2A%282.0-f%29++%29%29+%0A%0A%7D%0A)

Click here to start playing/generating the music:

![mad tea lab screenshot](madtealab-play.png)

In the end I move back and forth between MicroW8 and Mad Tea Lab, this is not ideal workflow, there is also the issue with float precision which makes this hard to test in js...

## [CurlyWas](https://github.com/exoticorn/curlywas) Source

	include "include/microw8-api.cwa"

	export fn upd() {

		cls(0x10);
		let inline t = time();
		let inline it = (t*88200_f) as i32; // time in samples

		let i = 12800;
		loop screen {

			let inline s = t + ((((i*512) & (3711*2 - ((3711_f * cos(max(6_f,t/2_f))) as i32)))) as f32)/64_f;  

			let inline x = snd((i>>1) + it + 1408                      )*s;
			let inline y = snd((i>>1) + it - 6336 - ((t*256_f) as i32) )*s;

			let inline rx = (x - y + 160_f) as i32;
			let inline ry = (y + x + 120_f) as i32;

			setPixel(rx, ry, (getPixel(rx, ry) + 3)|8);

			branch_if (i := i - 1) : screen
		}
		
	}

	export fn snd(sampleIndex: i32) -> f32 {

		let inline xx = sampleIndex as f32 / 88200_f;
		let warpedTime = select(xx > 16_f, xx - 8_f + pow(2_f, xx-34_f), 8_f * (xx/16_f)*(xx/16_f));  // xx * xx / 32_f 
		let x = warpedTime;
		x = fmod(x, 0.5); // loop half a second
		x = select(x<0.25, pow(x, 0.08349609375), x); // kick or bass? if kick, warp time
		x = x * 128_f;
		x = fmod(x, 2_f) - 1_f;
		let inline f = (1_f - cos(warpedTime/8_f)) * fmod(warpedTime, 1_f);  // f=0 filtered, f=1 unfiltered  
		let inline div=(f*x*x - 1_f);
		max(-1_f, min(1_f,   (x*x*x-x)/(select(i32.reinterpret_f32(div), div, 1_f))*(2_f-f)  )) // apply fake filter and distortion
		
	}