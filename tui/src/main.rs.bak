use std::{
    io::{stdout, Stdout},
    time::Duration,
};

use anyhow::Result;
use crossterm::{
    event::{self, Event, KeyCode, KeyEvent, KeyEventKind},
    terminal::{disable_raw_mode, enable_raw_mode, EnterAlternateScreen, LeaveAlternateScreen},
    ExecutableCommand,
};
use ratatui::prelude::*;
use ratatui::{
    backend::CrosstermBackend,
    style::{Style, Stylize},
    symbols::border,
    text::{Span, Text},
    widgets::{block::Title, Block, Borders, Paragraph},
    Terminal,
};

fn main() -> Result<()> {
    stdout().execute(EnterAlternateScreen)?;
    enable_raw_mode()?;

    let mut terminal = Terminal::new(CrosstermBackend::new(stdout()))?;
    terminal.clear()?;

    // loop {
    //     terminal.draw(|frame| {
    //         let area = frame.size();
    //         frame.render_widget(
    //             Paragraph::new("Hello, world! Press 'q' to quit!")
    //                 .white()
    //                 .on_blue(),
    //             area,
    //         );
    //     })?;

    //     if event::poll(Duration::from_millis(16))? {
    //         if let event::Event::Key(key) = event::read()? {
    //             if key.kind == KeyEventKind::Press && key.code == KeyCode::Char('q') {
    //                 break;
    //             }
    //         }
    //     }
    // }

    let mut app = App::default();
    let result = app.run(&mut terminal);

    stdout().execute(LeaveAlternateScreen)?;
    disable_raw_mode()?;

    result
}

#[derive(Debug, Default)]
struct App {
    counter: u8,
    exit_requested: bool,
}

impl App {
    pub fn run(&mut self, terminal: &mut Terminal<CrosstermBackend<Stdout>>) -> Result<()> {
        loop {
            if self.exit_requested {
                break;
            }

            terminal.draw(|frame| self.render_frame(frame))?;
            self.handle_events()?;
        }

        Ok(())
    }

    fn render_frame(&self, frame: &mut Frame) {
        frame.render_widget(self, frame.size());
    }

    fn handle_events(&mut self) -> Result<()> {
        match event::read()? {
            Event::Key(key) if key.kind == KeyEventKind::Press => self.handle_key_event(key),
            _ => {}
        };

        Ok(())
    }

    fn handle_key_event(&mut self, key_event: KeyEvent) {
        match key_event.code {
            KeyCode::Char('Q') => self.request_exit(),
            KeyCode::Left => self.decrement(),
            KeyCode::Right => self.increment(),
            _ => {}
        };
    }

    fn request_exit(&mut self) {
        self.exit_requested = true;
    }

    fn increment(&mut self) {
        self.counter += 1;
    }

    fn decrement(&mut self) {
        self.counter -= 1;
    }
}

impl Widget for &App {
    fn render(self, area: Rect, buf: &mut Buffer) {
        let title = Title::from(" Test ".bold());
        let instructions = Title::from(Line::from(vec![
            " Decrement ".into(),
            "<Left>".blue().bold(),
            " Increment ".into(),
            "<Right>".blue().bold(),
            " Quit ".into(),
            "<Q> ".blue().bold(),
        ]));

        let block = Block::default()
            .title(title.alignment(ratatui::layout::Alignment::Center))
            .title(
                instructions
                    .alignment(ratatui::layout::Alignment::Center)
                    .position(ratatui::widgets::block::Position::Bottom),
            )
            .borders(Borders::ALL)
            .border_set(border::THICK);

        let counter_text = Text::from(vec![Line::from(vec![
            "Value: ".into(),
            self.counter.to_string().yellow(),
        ])]);

        Paragraph::new(counter_text)
            .centered()
            .block(block)
            .render(area, buf);
    }
}
