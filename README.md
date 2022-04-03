# 컴퓨터 그래픽스 3차원 지형 응용 과제 _5팀

### 화면 구성 요소 설명 
```
중심이 지구본이기 때문에 별과 달을 사용하여 우주를 표현하였다.
그 후 밑에 3D 지형을 활용하여 우주에 대한 심오함을 나타내주기 위해 어두운 바다 색깔을 사용하여 
우주의 심오함과 달이 떠있는 별 하늘 사이에 있는 지구본을 표현했다.
```

### 소스 코드
```
//화면전환 키보드 선언
let cx=0;                   
let cy=0;                                     
let sliderGroup = [];
let img;
let a;
let X;
let Y;
let Z;
let centerX;
let centerY;
let centerZ;
let h = 20;

let cols, rows;
let scl = 20;
let w = 5000;
let hei = 1000;

let flying = 0;

let terrain = [];


function setup() {
  createCanvas(700, 700, WEBGL);
  cols = w / scl;
  rows = hei / scl;
  for (var x = 0; x-10 < cols; x++) {
  terrain[x] = [];
  for (var y = 0; y-10 < rows; y++) {
  terrain[x][y] = 0;            //현재 지정값 지정.                               
}
}
  noStroke();

  //create sliders
  //img를 넣음으로서 Material를 대처
  img = loadImage('https://upload.wikimedia.org/wikipedia/commons/thumb/7/78/Mollweide-projection.jpg/525px-Mollweide-projection.jpg');   
  
  //sliderGrop으로 모두 동일하게 값 선언.
  for (var i = 0; i < 6; i++) {
    if (i === 2) {
      sliderGroup[i] = createSlider(10, 400, 200);
    } else {
      sliderGroup[i] = createSlider(-400, 400, 0);
    }
    h = map(i, 0, 6, 5, 150);
    sliderGroup[i].position(10, height + h);
    sliderGroup[i].style('width', '80px');
  }
}

function draw() {
flying -= 0.01;
let yoff = flying;
for (var y = 0; y < rows; y++) {
let xoff = 0;
for (var x = 0; x < cols; x++) {
terrain[x][y] = map(noise(xoff, yoff), 0, 1, -50, 100);
xoff += 0.5;
}
yoff += 0.2;
}
background(0);

//pointLight을 사용하기 위한 마우스 선언.
  let locX = mouseX - width / 2;        
  let locY = mouseY - height / 2;  
  
translate(0, 50);
rotateX(PI / 3);
//fill(200, 200, 200);
translate(-w / 2, -hei / 2);
for (let y = 0; y < rows - 1; y++) {
beginShape(TRIANGLE_STRIP);

//beginShape 안에 pointLight을 사용하여 마우스 위치에 맞게 비춰지도록 함.
  pointLight(250, 250, 250, locX, locY, 50);   
  noStroke();
  sphere(60);       
                                                                                           
for (let x = 0; x < cols; x++) {
let v = terrain[x][y];
v = map(v, -50,100,255,0);
fill(v-964,v-126,v);
vertex(x * scl, y * scl, terrain[x][y]);
vertex(x * scl, (y + 1) * scl, terrain[x][y + 1]);
}
endShape();
}
translate(w / 2, hei / 2);

//sliderGroup에 camera로 축을 넣어주어 Slider를 움직이면 지구본이 움직이게끔 설정해준 기능.
  push();
  X = sliderGroup[0].value();
  Y = sliderGroup[1].value();
  Z = sliderGroup[2].value();
  centerX = sliderGroup[3].value();
  centerY = sliderGroup[4].value();
  centerZ = sliderGroup[5].value();
  camera(X, Y, Z, centerX, centerY, centerZ, 0, 1, 0);
  noStroke();
  texture(img);
  sphere(50);
  pop();
  
  //배경에 달을 넣어주기 위해 객체 생성을 하고 directionalLight()을 사용하여 그림자가 진 것을 표현.
  push();
  noStroke();
  translate(500,-1600);
  directionalLight(250, 660, -450);                            
  fill(250,155,0);
  sphere(75);                                       
  pop();
  
  push();
  translate(-3000,-6000);
  translate(w/2,hei/2);
  rotateX(-(PI/3));
  fill(255, 200);
  //ellipse(0,0,500,500);
  ellipse(random(width*8), random(height*2), random(60), random(60));
  pop();
  

//배경에 있는 별을 나타내주는 기능.
  push();
  translate(-500,-1000,400);
  translate(w/2,hei/2);
  rotateX(3*PI/2);
  fill(255, 200);
  ellipse(random(-w/2,w/2), random(-hei,hei/2), random(20), random(20));
  ellipse(random(-w/2,w/2), random(-hei,hei/2), random(10), random(10));
  ellipse(random(-w/2,w/2), random(-hei,hei/2), random(20), random(20));
  ellipse(random(-w/2,w/2), random(-hei,hei/2), random(20), random(20));
  pop();
 
//camera를 사용한 화면 전환 키보드 ( 'a', 'd', 's', 'w' 를 사용하여 양옆, 위아래로 움직일 수 있도록 만든 기능.)
 camera (1,0,(height / 2.0)/ tan (PI * 30.0 / 180.0),cx,cy,0,0,1,0);
 if(keyIsPressed){
  if(key == 'a'){
        cx -= 50;
      }
  if(key == 'd'){
        cx +=50;
      }
  if(key == 's'){
        cy +=50;
      }
  if(key == 'w'){
        cy -=50;
      }
  if(cx>350){
       cx=350;
     }
  if(cx<-350){
       cx=-350;
     }
  if(cy>300){
       cy=300;
     }
  if(cy<-450){
       cy=-450;
     }
   }
} 

//'P'를 누르면 지구본의 위치를 캡처할 수 있도록 만든 저장 기능.
function keyPressed(){
  if(key == 'p'|| key == 'P'){                 
    save("data/testImg.png");  
  }
}
```

### 소감문

1. 유채린_ 20201480
```
팀원들과 함께 3차원 지형에 지구와 달이 있는 배경에 별이 지구 주위로 반짝이면 좋겠다고 생각하여 아이디어를 내었고 
p5js의 유튜브나 레퍼런스 자료등을  활용하여 random함수와 fill함수를 통해서 실제로 별이 구현이 되어 신기했습니다. 
처음에는 별이 하늘이 아니라 바닥에 반짝거려서 실패했지만 팀원분들이 의견을 내주시고 함께 코드를 수정하여서 별이 성공적으로 구현되어 기뻤습니다. 
또한 팀원들과 협업을 하면서 생각하지 못했던 새로운 아이디어를 공유해서 좋았습니다. 협업을 하면서  키보드로 화면전환을 할 수 있는 기능과 화면을 키보드 S를 눌렀을 때 
저장되게 할 수 있는것 pointlight 함수를 사용하여 지구를 마우스가 있는 곳의 위치에 따라서 어둡게 또는 밝게 조정할 수 있는 것을 알게 되어 
이번 팀플을 통해서 그래픽의 기능이나 함수의 역할에 대해 더 자세하게 알게되어 의미있는 시간이였습니다. 
앞으로 그래픽관련 공부를 더 열심히 해야되겠다고 생각하였습니다.
```

2. 최민정_ 20201115
```
1학년 때 심재창 교수님 수업을 들었었습니다. 당시에 창의공학 및 전공필수 수업을 교수님께 들었었는데 그게 너무 좋은 기억으로 남아있어 이번 컴퓨터 그래픽스도 
정말 즐겁게 진행했던 것 같습니다. 저는 자바스크립트를 이 수업에서 처음 접해봤습니다. 그래서 두려움이 컸는데 양질의 강의와 제가 모르겠는 애매한 부분은 
교수님이 따로 좋은 강의 영상을 추천해 주셔서 이번 팀플도 원활히 진행할 수 있었던 것 같습니다. 
특히나 이렇게 창의적으로 팀원들과 무언가를 직접 상의해서 제작하는 과정이 너무나 흥미로웠고 유익했다고 생각합니다. 
다양한 영상과 프리소스를 참고해서 제가 모르는 부분을 보완했고, 팀원들과의 소통을 통해 부족한 점을 채워나가며 진행했었습니다. 
서로 모르는 점을 묻고 해결하는 과정이 많아서 진행하는데 큰 어려움이 없었습니다. 
무엇보다 제가 프로세싱에 관심이 있고 그래픽 디자인 쪽에 흥미가 컸기 때문에 이런 과정에서 많은 것을 배워간 것 같습니다. 앞으로도 더 열심히 하겠습니다! 
감사합니다.
```

3. 조은경_ 20201110
```
과제가 주어졌을 때 아이디어가 떠오르지 않아 무작정 레퍼런스를 실행해 보았습니다. 
레퍼런스를 둘러본 결과 나온 아이디어가 pointLight()를 이용하여 손전등 만들기, 불이 비치는곳만 시야 파악이 되게 제작, directionalLight()로 해 뜨고 지기, 
camera()와 key입력으로 wasd로 시점전환, terrain[]이용하여 일정 간격으로 구조물 세우기 등을 아이디어로 구상해 보았습니다. 
처음에는 테마도 정해지지 않은 빈약한 아이디어였으나, 팀원과 아이디어 공유를 하며 점점 방향을 잡아갔고 그것들을 토대로 다 같이 코드를 짜 과제를 수행하였습니다. 
확실히 팀프로젝트를 하니 개인이 하는 것보다 아이디어가 잘 나와서 재미있었습니다. 
또, 코드 에러가 났을 땐 도움도 받아가며 진행하니 좋았습니다. 이것저것 아이디어를 공유하며 추가하다 보니 나중에 생각난 아이디어들이 있으나 
과제 주제에 맞지 않는 것 같다는 점과 시간상의 이유로 넣지 못 한건 아쉽습니다. 그래도 다음기회에 개인적으로 제작해볼까 합니다. 
팀원들의 도움을 받으며 팀프로젝트를 해결해 나간 것으로 좋은 경험을 얻었다고 생각합니다.
```

4. 원지은_ 20201590
```
이번에 그래픽스 팀플을 하면서 많이 느낀 것은 깊게 생각하고 찾아본다면 제가 만들 수 있는 것을 만들 수 있다고 생각했습니다. 
이번에 3D지형에 응용을 하면서 우주배경을 전체적인 분위기로 하였는데 뒤에 검은 배경이라 달을 넣고 싶었습니다. 
그래서 객체를 생성하고 만들어서 그림자가 진 것을 directionalLight()을 이용하여 사용하였는데 제가 원하는 것이 되고 뜨니까 
너무 예쁘고 재미있었습니다. 하지만 정작 뒤에 이미지를 넣어서 더 우주같은 느낌을 만들고 싶었지만 팀원들과 함께 노력했고 
개개인마다 열심히 고민하고 실행보았지만 잘 되지 않아 이것은 좀 많이 아쉬웠습니다. 
다음에는 이런 아쉬운 점을 보안하고 더 마음에 드는 작품을 만들고 싶습니다.
```

5. 이하은_ 20201591
```
이번 3차원 지형에 소스코드를 사용하여 팀원들과 여러 코드들을 응용해보고, 실행시키면서 느낀 점은 
3D 지형에 pointLight()를 사용하여 마우스가 위치하는 곳에 빛을 만들어주기로 하게 되어서  pointLight()를 여러 위치에 대입 시키며 실행 결과를 확인해보았는데 
실행은 되지만 용량 문제로 렉이 걸리면서 원만하게 나오지 않아 막막했었습니다. 하지만 예제 파일들을 찾아보면서 위치에 맞게  실행해주면서 든 생각이 
전체적으로 밝게 비추는 것이 아니라 달이 떠있는 별 하늘에서 비춰지는 모습으로 만들면 좋겠다고 생각하였는데 생각과 맞게 잘 대입되어 뿌듯하였고, 
이번 과제를 통해 이해 되지 않았던 코드들과 팀원들이 추가한 새로운 코드들을 실행결과와 함께 확인하면서 확실히 이해를 할 수 있었고 
새로운 코드 진행들에 대해 공부할 수 있었던 계기가 되었습니다.
하지만 아직 코드 사용에 대한 미숙한 점때문에 원하고자 하는 결과를 완벽히 이뤄내진 못했지만 
그래픽스 시간과 개인 시간을 더해 더 공부하여 미숙한 점을 보완하여 더 나은 결과를 만들고자 노력해야겠다고 생각했습니다. 
그리고 여러 사람들과 함께 하는 과제이다 보니 더 신경이 쓰였지만 팀원들 모두 적극적으로 의견을 내고 모아 해결할 수 있어서 
여러모로 의미가 컸던 시간이였던 거 같습니다.
```

