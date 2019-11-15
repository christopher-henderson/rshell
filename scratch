use crate::errors::*;
use std::process::Stdio;


//https://tiswww.case.edu/php/chet/bash/bashref.html#Shell-Syntax

#[cfg(test)]
mod testsss {
    use super::*;

    #[test]
    fn basic() {
//        redo("ls -l || ls -azl && ls -z || ls");
//        redo("ls -l && ls -z || ls -a");
        println!("{:?}", redo("gasdsreps Cargo"));
    }
}

//3.1.1 Shell Operation
//
//The following is a brief description of the shell’s operation when it reads and executes a command. Basically, the shell does the following:
//
//    Reads its input from a file (see Shell Scripts), from a string supplied as an argument to the -c invocation option (see Invoking Bash), or from the user’s terminal.
//    Breaks the input into words and operators, obeying the quoting rules described in Quoting. These tokens are separated by metacharacters. Alias expansion is performed by this step (see Aliases).
//    Parses the tokens into simple and compound commands (see Shell Commands).
//    Performs the various shell expansions (see Shell Expansions), breaking the expanded tokens into lists of filenames (see Filename Expansion) and commands and arguments.
//    Performs any necessary redirections (see Redirections) and removes the redirection operators and their operands from the argument list.
//    Executes the command (see Executing Commands).
//    Optionally waits for the command to complete and collects its exit status (see Exit Status).

pub fn redo(line: &str) -> Result<()> {
    let t = match shlex::split(line) {
        None => return Ok(()),
        Some(tokens) => tokens,
    };
    let mut tokens: Vec<&str> = t.iter().map(|s| s.as_str()).collect();
    tokens.reverse();
    let mut current: Option<std::process::Command> = None;
    let mut succeed: Option<bool> = None;
    let mut stdin: Option<std::process::ChildStdout> = None;
    let mut token = tokens.pop();
    loop {
        match (token, &mut current, succeed) {
            (None, None, None) => return Ok(()),
            (None, None, Some(true)) => return Ok(()),
            (None, None, Some(false)) => return Ok(()),
            (None, Some(cmd), None) => {
                cmd.spawn().chain_err(|| "")?.wait();
                current = None;
                return Ok(());
            }
            (None, Some(cmd), Some(true)) => return Ok(()),
            (None, Some(cmd), Some(false)) => {
                cmd.spawn().chain_err(|| "")?.wait();
                current = None;
                return Ok(());
            }
            (Some("&&"), None, None) => return Ok(()),
            (Some("&&"), None, Some(true)) => {
                succeed = None;
            }
            (Some("&&"), None, Some(false)) => return Ok(()),
            (Some("&&"), Some(cmd), None) => {
                if !cmd
                    .spawn()
                    .chain_err(|| "")?
                    .wait()
                    .chain_err(|| "")?
                    .success()
                {
                    return Ok(());
                }
                current = None;
            }
            (Some("&&"), Some(cmd), Some(true)) => {
                current = None;
                succeed = None;
            }
            (Some("&&"), Some(cmd), Some(false)) => {
                if !cmd
                    .spawn()
                    .chain_err(|| "")?
                    .wait()
                    .chain_err(|| "")?
                    .success()
                {
                    return Ok(());
                }
                current = None;
            }
            (Some("||"), None, None) => return Ok(()),
            (Some("||"), None, Some(true)) => return Ok(()),
            (Some("||"), None, Some(false)) => return Ok(()),
            (Some("||"), Some(cmd), None) => {
                succeed = Some(
                    cmd.spawn()
                        .chain_err(|| "")?
                        .wait()
                        .chain_err(|| "")?
                        .success(),
                );
                current = None;
            }
            (Some("||"), Some(cmd), Some(true)) => {
                current = None;
            }
            (Some("||"), Some(cmd), Some(false)) => {
                succeed = Some(
                    cmd.spawn()
                        .chain_err(|| "")?
                        .wait()
                        .chain_err(|| "")?
                        .success(),
                );
                current = None;
            }
            (Some(">"), None, None) => (),
            (Some(">"), None, Some(true)) => (),
            (Some(">"), None, Some(false)) => (),
            (Some(">"), Some(cmd), None) => (),
            (Some(">"), Some(cmd), Some(true)) => (),
            (Some(">"), Some(cmd), Some(false)) => (),
            (Some(">>"), None, None) => (),
            (Some(">>"), None, Some(true)) => (),
            (Some(">>"), None, Some(false)) => (),
            (Some(">>"), Some(cmd), None) => (),
            (Some(">>"), Some(cmd), Some(true)) => (),
            (Some(">>"), Some(cmd), Some(false)) => (),
            (Some("&"), None, None) => (),
            (Some("&"), None, Some(true)) => (),
            (Some("&"), None, Some(false)) => (),
            (Some("&"), Some(cmd), None) => (),
            (Some("&"), Some(cmd), Some(true)) => (),
            (Some("&"), Some(cmd), Some(false)) => (),
            (Some(";"), None, None) => (),
            (Some(";"), None, Some(true)) => (),
            (Some(";"), None, Some(false)) => (),
            (Some(";"), Some(cmd), None) => (),
            (Some(";"), Some(cmd), Some(true)) => (),
            (Some(";"), Some(cmd), Some(false)) => (),
            (Some("|"), None, None) => (),
            (Some("|"), None, Some(true)) => (),
            (Some("|"), None, Some(false)) => (),
            (Some("|"), Some(cmd), None) => {
                cmd.stdout(Stdio::piped());
                stdin = cmd.spawn().chain_err(|| "")?.stdout;
                current = None;
            },
            (Some("|"), Some(cmd), Some(true)) => (),
            (Some("|"), Some(cmd), Some(false)) => (),
            (Some(token), None, None) => {
                let mut cmd = std::process::Command::new(token);
                cmd.stdout(Stdio::inherit())
                    .stderr(Stdio::inherit())
                    .envs(std::env::vars());
                match stdin {
                    Some(input) => {
                        cmd.stdin(input);
                        stdin = None;
                    },
                    None => {
                        cmd.stdin(Stdio::inherit());
                    }
                }
                current = Some(cmd);
            }
            (Some(token), None, Some(true)) => {}
            (Some(token), None, Some(false)) => {
                let mut cmd = std::process::Command::new(token);
                cmd.stdout(Stdio::inherit())
                    .stderr(Stdio::inherit())
                    .envs(std::env::vars());
                match stdin {
                    Some(input) => {
                        cmd.stdin(input);
                        stdin = None;
                    },
                    None => {
                        cmd.stdin(Stdio::inherit());
                    }
                }
                current = Some(cmd);
            }
            (Some(token), Some(cmd), None) => {
                cmd.arg(token);
            }
            (Some(token), Some(cmd), Some(true)) => {
                cmd.arg(token);
            }
            (Some(token), Some(cmd), Some(false)) => {
                cmd.arg(token);
            }
        }
        token = tokens.pop();
    }
    Ok(())
}

