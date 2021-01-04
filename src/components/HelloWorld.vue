<template>
  <div v-show="playing">
    <div class='center' v-for="(row, i) in grid" :key='i'>
      <div class='tcenter' v-for="(elem, j) in row" :key='j'>
        <div 
          class="square"
          v-bind:style="styled(elem)"
        >
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .square {
    width: 20px;
    height: 20px;
    /*border: solid 0.5px white;*/
  }
  /*
  .empty { background-color: black; }
  .food { background-color: tomato; }
  .snake { background-color: turquoise; }
  .row { display: flex; }
  */
  
  .center {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .tcenter {
    text-align: center;
  }
</style>

<script lang="ts">

  import { ref, Ref } from 'vue'
  
  type Num = number
  type Pos = [Num, Num]

  const T = 100
  const size = 40

  enum Cell { Empty, Snake, Food }

  function randomPos(): Pos {
    const rand = () => Math.floor(Math.random() * size)
    return [rand(), rand()]
  }


  type Dir = [Num, Num]

  const Dirs = {
    "Up":    [-1,  0] as Dir,
    "Down":  [ 1,  0] as Dir,
    "Left":  [ 0, -1] as Dir,
    "Right": [ 0,  1] as Dir,
  }

  let currDir = Dirs.Up

 
  export default {
    name: 'HelloWorld',
    data() {
      return {
        input: "",
        grid: [...Array(size)].map(() => [...Array(size)].map(() => Cell.Empty)),
        snake: [[size/2, size/2],[size/2, size/2]] as Pos[],
        dir: Dirs.Up,
        playing: true,
      }
    },

    methods: {
      addFood() {
        let r, c
        [r, c] = randomPos()
        this.grid[r][c] = Cell.Food
      },
      keyDown(event: KeyboardEvent) {
      },
      styled(elem: Cell) {
        switch (elem) {
          case Cell.Empty:
            return { 'background-color': 'black' }
          case Cell.Snake:
            return { 'background-color': 'tomato' }
          case Cell.Food:
            return { 'background-color': 'turquoise' }
        }
      },
      move(dir: Dir) {
        const sum = (a: Pos, b: Pos): Pos => [a[0]+b[0], a[1]+b[1]]
        const outside = (e: Num): boolean => e < 0 || e >= size
        const includes = (list: Pos[], pos: Pos): Boolean => {
          for (let e of list) {
            if (e == pos) return true
          } return false
        }

        const next: Pos = sum(this.snake[0], dir)

        console.log("next")
        console.log(next)
        console.log("snake")
        console.log(this.snake.toString())
        console.log("includes")
        console.log(includes(this.snake, next))

        if (next.some((elem) => outside(elem))) this.playing = false
        if (this.snake.includes(next)) this.playing = false
        else {
          let eat: boolean = (this.grid[next[0]][next[1]] == Cell.Food)
          if (!eat) {
            let last = this.snake.pop()
            if (last != undefined) this.grid[last[0]][last[1]] = Cell.Empty
          } else {
            this.grid[next[0]][next[1]] = Cell.Snake
            this.addFood()
          }
          this.snake.unshift(next)
          this.grid[next[0]][next[1]] = Cell.Snake
        }
      }
    },
    created() {

      this.addFood()
      window.addEventListener('keydown', (event) => {
        switch (event.key) {
          case "w": this.dir = Dirs.Up; break;
          case "a": this.dir = Dirs.Left; break;
          case "s": this.dir = Dirs.Down; break;
          case "d": this.dir = Dirs.Right; break;
        }
      })
      window.setInterval(() => {
        //console.log(
          this.move(this.dir)
          //console.log(this.snake.length)
          //)
        //console.log(this.snake.toString())
        //console.log(this.grid.toString())
      }, T)
    }
  }
</script>
