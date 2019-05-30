<template>
  <div id="game">
  </div>
</template>

<script>
import Phaser from 'phaser'
import wall from '../assets/wall.png'
import ground from '../assets/ground.png'
import player from '../assets/player.png'
import coin from '../assets/coin.png'
import enemy from '../assets/enemy.png'
import soundDead from '../assets/dead.mp3'
import soundCoin from '../assets/coin.mp3'
import buttonPlay from '../assets/buttonplay.png'
import logo from '../assets/logo.png'
import gameOver from '../assets/go.jpg'
import buttonHome from '../assets/home.png'
let score = 0
let scoreText
let livesText

function takeCoin (player, coin) {
  // todo -> millorar amb una animacio i executar so al agafar la moneda
  coin.disableBody(true, true)
  score = score + 10
  scoreText.setText('Score: ' + score)
  this.sound.play('soundCoin')
}

function die (player, enemy) {
  // todo reiniciar nivell
  player.lives = player.lives - 1
  player.scene.cameras.main.shake(500)
  this.sound.play('soundDead')
  player.x = 500 / 2
  player.y = 200 / 2 - 50
  livesText.setText('Lives: ' + player.lives)
  if (player.lives == 0) {
    this.scene.start('sceneGameOver')
  }
}
export default {
  name: 'Game',
  created () {
    // Phaser 3.0 -> phaser
    let config = {
      type: Phaser.AUTO,
      width: 500,
      height: 200,
      physics: {
        default: 'arcade',
        arcade: {
          gravity: { y: 200 }
        }
      },
      scene: [SceneLoading, SceneMenu, ScenePlay, SceneGameOver]
    }
    new Phaser.Game(config)
  }
}

class SceneLoading extends Phaser.Scene {
  constructor () {
    super({ key: 'sceneLoading' })
  }

  preload () {
    // carregar assets -> imatges audios videos //
    this.load.image('wall', wall)
    this.load.image('logo', logo)
    this.load.image('ground', ground)
    this.load.image('coin', coin)
    this.load.image('enemy', enemy)
    this.load.image('gameOver', gameOver)
    this.load.spritesheet('player', player, { frameWidth: 28, frameHeight: 22 })
    this.load.spritesheet('buttonPlay', buttonPlay, { frameWidth: 613, frameHeight: 242 })
    this.load.spritesheet('buttonHome', buttonHome, { frameWidth: 222, frameHeight: 215 })
    this.load.audio('soundDead', soundDead)
    this.load.audio('soundCoin', soundCoin)
    // Joystick virtual
    var url

    url = 'https://raw.githubusercontent.com/rexrainbow/phaser3-rex-notes/master/plugins/dist/rexvirtualjoystickplugin.min.js'
    this.load.plugin('rexvirtualjoystickplugin', url, true)

    // D'aqui fins al final del preload es tot la barra de carrega del principi
    var progressBar = this.add.graphics()
    var progressBox = this.add.graphics()
    progressBox.fillStyle(0x222222, 0.8)

    var width = this.game.renderer.width * 0.5
    var height = this.game.renderer.height * 0.5
    progressBox.fillRect(window.x = width * 0.5 - 165, window.y = height * 0.5 - 75, 320, 50)
    var loadingText = this.make.text({
      x: width * 0.5,
      y: height * 0.5 - 100,
      text: 'Loading...',
      style: {
        font: '20px monospace',
        fill: '#ffffff'
      }
    })
    loadingText.setOrigin(0.5, 0.5)

    var percentText = this.make.text({
      x: width * 0.5,
      y: height * 0.5 - 50,
      text: '0%',
      style: {
        font: '18px monospace',
        fill: '#ffffff'
      }
    })
    percentText.setOrigin(0.5, 0.5)

    var assetText = this.make.text({
      x: width * 0.5,
      y: height * 0.5 - 5,
      text: '',
      style: {
        font: '18px monospace',
        fill: '#ffffff'
      }
    })

    assetText.setOrigin(0.5, 0.5)

    this.load.on('progress', function (value) {
      percentText.setText(parseInt(value * 100) + '%')
      progressBar.clear()
      progressBar.fillStyle(0xffffff, 1)
      progressBar.fillRect(window.x = width * 0.5 - 165, window.y = height * 0.5 - 65, 300 * value, 30)
    })

    this.load.on('fileprogress', function (file) {
      assetText.setText('Loading asset: ' + file.key)
    })

    this.load.on('complete', function () {
      progressBar.destroy()
      progressBox.destroy()
      loadingText.destroy()
      percentText.destroy()
      assetText.destroy()
    })
  }
  create () {
    this.scene.start('sceneMenu')
  }
}

class SceneMenu extends Phaser.Scene {
  constructor () {
    super({ key: 'sceneMenu' })
  }

  preload () {

  }

  create () {
    this.cameras.main.backgroundColor.setTo(52, 152, 219)
    let logo = this.add.sprite(this.game.renderer.width * 0.5, this.game.renderer.height * 0.5 - 40, 'logo').setDepth(1)

    let buttonPlay = this.add.sprite(this.game.renderer.width * 0.5, this.game.renderer.height * 0.5 + 40, 'buttonPlay').setDepth(1)
    buttonPlay.displayHeight = 60
    buttonPlay.displayWidth = 150

    buttonPlay.setInteractive()
    buttonPlay.on('pointerup', () => {
      this.scene.start('scenePlay')
    })
  }
}

class SceneGameOver extends Phaser.Scene {
  constructor () {
    super({ key: 'sceneGameOver' })
  }
  create () {
    this.cameras.main.backgroundColor.setTo(0, 0, 0)
    let logo = this.add.sprite(this.game.renderer.width * 0.5, this.game.renderer.height * 0.5 - 30, 'gameOver').setDepth(1)
    logo.displayHeight = 120
    logo.displayWidth = 300

    let buttonHome = this.add.sprite(this.game.renderer.width * 0.5 + 50, this.game.renderer.height * 0.5 + 60, 'buttonHome').setDepth(1)
    buttonHome.displayHeight = 50
    buttonHome.displayWidth = 60

    buttonHome.setInteractive()
    buttonHome.on('pointerup', () => {
      this.scene.start('sceneMenu')
    })
    let buttonPlay = this.add.sprite(this.game.renderer.width * 0.5 - 50, this.game.renderer.height * 0.5 + 60, 'buttonPlay').setDepth(1)
    buttonPlay.displayHeight = 50
    buttonPlay.displayWidth = 120

    buttonPlay.setInteractive()
    buttonPlay.on('pointerup', () => {
      this.scene.start('scenePlay')
      score = 0
    })
  }
}

class ScenePlay extends Phaser.Scene {
  constructor () {
    super({ key: 'scenePlay' })
  }

  preload () {

  }

  create () {
    // Afegim el so
    this.sound.add('soundDead')
    this.sound.add('soundCoin')

    this.cameras.main.backgroundColor.setTo(52, 152, 219)

    this.level = this.physics.add.staticGroup()

    this.level.create(500 / 2 - 160, 200 / 2, 'wall')
    this.level.create(500 / 2 + 160, 200 / 2, 'wall')
    this.level.create(500 / 2, 200 / 2 + 30, 'ground')

    // PLAYER

    this.player = this.physics.add.sprite(500 / 2, 200 / 2 - 50, 'player')
    this.player.setCollideWorldBounds(true)
    this.player.setBounce(0.2)
    this.physics.add.collider(this.player, this.level)
    this.player.lives = 3

    // DEFINE SOUNDS
    // this.dustSound = this.add.audio('dust', 0.1)

    this.cursors = this.input.keyboard.createCursorKeys()

    this.anims.create({
      key: 'idle',
      frames: this.anims.generateFrameNumbers('player', { start: 3, end: 5 }),
      frameRate: 5,
      repeat: -1
    })

    this.coins = this.physics.add.group()

    this.coins.create(140, 200 / 2, 'coin')
    this.coins.create(165, 200 / 2, 'coin')
    this.coins.create(190, 200 / 2, 'coin')

    this.physics.add.collider(this.coins, this.level)
    this.physics.add.overlap(this.coins, this.player, takeCoin, null, this)

    // ENEMIES

    this.enemies = this.physics.add.group()
    this.enemies.create(500 / 2 + 130, 200 / 2, 'enemy')
    this.physics.add.collider(this.enemies, this.level)
    this.physics.add.overlap(this.player, this.enemies, die, null, this)

    // SCORE & LIVES
    scoreText = this.add.text(20, 20, 'Score: 0', { fontSize: '18px', fill: '#000' })
    livesText = this.add.text(20, 40, 'Lives: 3', { fontSize: '18px', fill: '#000' })

    // LOOSER TEXT
    this.loserText = this.add.text(500 / 2 - 50, 200 / 2 - 50, 'YOU DIED', { fontSize: '30px', fill: '#830' }).setVisible(false)
    this.cameras.main.on('camerashakestart', () => {
      this.loserText.setVisible(true)
    })
    this.cameras.main.on('camerashakecomplete', () => {
      this.loserText.setVisible(false)
    })
  }
  update () {
    this.player.anims.play('idle', true)

    if (this.cursors.left.isDown) {
      this.player.setVelocityX(-160)
      this.player.setFrame(2)
    } else if (this.cursors.right.isDown) {
      this.player.setVelocityX(160)
      this.player.setFrame(1)
    } else {
      this.player.setVelocityX(0)
    }

    if (this.cursors.up.isDown && this.player.body.touching.down) {
      this.player.setVelocityY(-160)
    }
  }
}
</script>
