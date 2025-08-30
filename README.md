# OneAv-Block-Game
Play my game!
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>One AV Match-3</title>
<style>
  body { background:#0f1115; color:#fff; font-family:sans-serif; display:flex; flex-direction:column; align-items:center; margin:0; padding:20px;}
  h1 { margin-bottom:10px; }
  #score { font-size:1.2em; margin-bottom:10px; }
  #board { display:grid; grid-template-columns: repeat(8, 50px); grid-gap:5px; }
  .tile { width:50px; height:50px; border-radius:8px; display:flex; align-items:center; justify-content:center; font-weight:bold; color:#000; user-select:none; }
  .R { background:red; }
  .G { background:green; }
  .W { background:white; }
</style>
</head>
<body>

<h1>One AV Match-3</h1>
<div id="score">One AV Points: 0</div>
<div id="board"></div>
<p>Swipe tiles to swap and make 3+ in a row. Each clear = +10 One AV Points!</p>

<script>
const ROWS=8, COLS=8;
const COLORS=['R','G','W'];
let grid=[], score=0, selected=null;
const board=document.getElementById('board');
const scoreEl=document.getElementById('score');

function randColor(){ return COLORS[Math.floor(Math.random()*COLORS.length)]; }

function initGrid(){
  grid=[];
  for(let r=0;r<ROWS;r++){
    grid[r]=[];
    for(let c=0;c<COLS;c++){
      grid[r][c]=randColor();
    }
  }
}

function render(){
  board.innerHTML='';
  for(let r=0;r<ROWS;r++){
    for(let c=0;c<COLS;c++){
      const tile=document.createElement('div');
      tile.className='tile '+grid[r][c];
      tile.dataset.row=r;
      tile.dataset.col=c;
      tile.addEventListener('click',()=>selectTile(r,c));
      board.appendChild(tile);
    }
  }
}

function selectTile(r,c){
  if(!selected){
    selected={r,c};
  }else{
    swap(selected.r,selected.c,r,c);
    selected=null;
  }
}

function swap(r1,c1,r2,c2){
  if(Math.abs(r1-r2)+Math.abs(c1-c2)===1){
    [grid[r1][c1],grid[r2][c2]]=[grid[r2][c2],grid[r1][c1]];
    if(!clearMatches()){ // no match → swap back
      [grid[r1][c1],grid[r2][c2]]=[grid[r2][c2],grid[r1][c1]];
    }
    render();
  }
}

function clearMatches(){
  let matched=false;
  let toClear=[];
  // horizontal
  for(let r=0;r<ROWS;r++){
    let run=1;
    for(let c=1;c<COLS;c++){
      if(grid[r][c]===grid[r][c-1]) run++; else run=1;
      if(run>=3){
        for(let k=0;k<run;k++) toClear.push([r,c-k]);
      }
    }
  }
  // vertical
  for(let c=0;c<COLS;c++){
    let run=1;
    for(let r=1;r<ROWS;r++){
      if(grid[r][c]===grid[r-1][c]) run++; else run=1;
      if(run>=3){
        for(let k=0;k<run;k++) toClear.push([r-k,c]);
      }
    }
  }
  if(toClear.length>0){
    matched=true;
    toClear.forEach(([r,c])=>{ grid[r][c]=null; });
    score+=10;
    scoreEl.textContent='One AV Points: '+score;
    dropTiles();
  }
  return matched;
}

function dropTiles(){
  for(let c=0;c<COLS;c++){
    for(let r=ROWS-1;r>=0;r--){
      if(grid[r][c]===null){
        for(let k=r-1;k>=0;k--){
          if(grid[k][c]!==null){
            grid[r][c]=grid[k][c];
            grid[k][c]=null;
            break;
          }
        }
      }
    }
    for(let r=0;r<ROWS;r++){
      if(grid[r][c]===null) grid[r][c]=randColor();
    }
  }
  setTimeout(()=>{
    if(clearMatches()) render();
  },200);
}

// Initialize
initGrid();
render();
</script>

</body>
</html>