// Importar Phaser
import Phaser from 'phaser';

// Criar a classe do jogo
class JornadaCampoCidade extends Phaser.Scene {
  constructor() {
    super('JornadaCampoCidade');
  }

  // Criar o jogo
  create() {
    // Configurações do jogo
    this.config = {
      type: Phaser.CANVAS,
      parent: 'game',
      width: 800,
      height: 600,
    };

    // Criar o grupo de personagens
    this.personagens = this.add.group();

    // Criar o grupo de oportunidades
    this.oportunidades = this.add.group();

    // Criar o personagem principal
    this.personagem = this.add.sprite(100, 100, 'personagem');
    this.personagem.setScale(0.5);
    this.personagem.setCollideWorldBounds(true);
    this.personagens.add(this.personagem);

    // Criar a cidade
    this.cidade = this.add.sprite(700, 100, 'cidade');
    this.cidade.setScale(0.5);
    this.cidade.setCollideWorldBounds(true);
    this.personagens.add(this.cidade);

    // Criar os botões de ação
    this.botaoColher = this.add.image(100, 200, 'botaoColher');
    this.botaoColher.setScale(0.5);
    this.botaoColher.setInteractive();
    this.botaoColher.on('pointerdown', this.colherOportunidade, this);

    this.botaoMover = this.add.image(100, 300, 'botaoMover');
    this.botaoMover.setScale(0.5);
    this.botaoMover.setInteractive();
    this.botaoMover.on('pointerdown', this.moverPersonagem, this);

    // Criar o sistema de pontuação
    this.pontos = 0;
    this.pontosText = this.add.text(100, 50, 'Pontos: 0', { fontSize: 24 });
  }

  // Carregar recursos
  preload() {
    this.load.image('personagem', 'assets/personagem.png');
    this.load.image('cidade', 'assets/cidade.png');
    this.load.image('botaoColher', 'assets/botaoColher.png');
    this.load.image('botaoMover', 'assets/botaoMover.png');
    this.load.image('oportunidade', 'assets/oportunidade.png');
  }

  // Criar oportunidades
  create() {
    // Criar oportunidades aleatórias
    for (let i = 0; i < 10; i++) {
      const oportunidade = this.add.sprite(
        Phaser.Math.Between(0, this.config.width),
        Phaser.Math.Between(0, this.config.height),
        'oportunidade'
      );
      oportunidade.setScale(0.5);
      oportunidade.setCollideWorldBounds(true);
      this.oportunidades.add(oportunidade);
    }
  }

  // Atualizar o jogo
  update(time, delta) {
    // Verificar colisões com oportunidades
    this.oportunidades.children.iterate((oportunidade) => {
      if (this.personagem.body.overlap(oportunidade.body)) {
        // Colher oportunidade
        this.colherOportunidade(oportunidade);
      }
    });

    // Verificar colisões com a cidade
    if (this.personagem.body.overlap(this.cidade.body)) {
      // Chegar à cidade
      this.chegarACidade();
    }
  }

  // Colher oportunidade
  colherOportunidade(oportunidade) {
    // Incrementar pontos
    this.pontos += 1;
    this.pontosText.setText(`Pontos: ${this.pontos}`);

    // Remover oportunidade
    this.oportunidades.remove(oportunidade, true);
  }

  // Mover personagem
  moverPersonagem() {
    // Mover personagem para a direita
    this.personagem.body.x += 10;
  }

  // Chegar à cidade
  chegarACidade() {
    // Mostrar mensagem de vitória
    this.add.text(100, 100, 'Você chegou à cidade!', { fontSize: 36 });

    // Finalizar o jogo
    this.scene.stop();
  }
}

// Criar o jogo
const config = {
  type: Phaser.AUTO,
  width: 800,
  height: 600,
  scene: JornadaCampoCidade,
};

const game = new Phaser.Game(config);
