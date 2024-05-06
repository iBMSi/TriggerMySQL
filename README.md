# TriggerMySQL
-----------------

Códido em MySql para criação de triggers que checam a minutagem de filmes e outro para exceção 
---------------------------------------------------------------------------------------------------------------------

create database Exercicio_Trigger;

use Exercicio_trigger;

-- EXEMPLO 1

-- Criação da tabela "Pedidos"

 CREATE TABLE Pedidos (
IDPedido INT AUTO_INCREMENT PRIMARY KEY,
DataPedido DATETIME,
NomeCliente VARCHAR (188)
);

------------------------------------------------

-- Inserção de dados de exemplo 

INSERT INTO Pedidos (DataPedido, NomeCliente) VALUES

(now(), 'Cliente 1'),

(NOW(), 'Cliente 2'),

(NOW(), 'Cliente 3');

--------------------------------------------------------------

-- Inserção de um novo registro na tabela "Pedidos" sem fornecer data

insert into Pedidos (NomeCliente) values ('Novo Cliente');

-------------------------------------------------------------------------


-- Consulta para verificar os resultados

select * from Pedidos;

--------------------------------------------

-- EXEMPLO 2

create table Filmes (
id		int	primary key	auto_increment,
titulo	varchar(60),
minutos	int
);


-- Trigger

delimiter //

create trigger chk_minutos before insert on Filmes

	for each row
	begin
		if new.minutos < 0 then 
			set new.minutos = null;
		end if;
	end;
 
//
delimiter ;

---------------------------------------------------------------


-- inserção do trigger 2

insert into Filmes (titulo, minutos) values ("O labirinto do fauno"	   ,	240);

insert into Filmes (titulo, minutos) values ("Metropole"			   ,	 90);

insert into Filmes (titulo, minutos) values ("A lista"				   ,	-88);

insert into Filmes (titulo, minutos) values ("The terrible trigger"    ,	120); 

insert into Filmes (titulo, minutos) values ("O alto da compadecida"   ,	135); 

insert into Filmes (titulo, minutos) values ("Faroeste caboclo"        ,	110);

insert into Filmes (titulo, minutos) values ("The matrix" 			   ,	  0);

insert into Filmes (titulo, minutos) values ("Blade runner"			   ,	120);

-----------------------------------------------------------------------------------


-- exception 
﻿
delimiter //

create trigger chk_minutos2 before insert on Filmes

for each row

	begin
		if new.minutos < 0 then
			-- Lançar um Erro
			signal SQLSTATE '45000' -- exceção não tratata
			set MESSAGE_TEXT = "Valor inválido para minutos", -- código de erro pra controle
            MYSQL_ERRNO = 2022; -- código de erro para controle 
end if;
end; 

//
delimiter ;

---------------------------------------------------------------------------------------------

-- triger exemplo 2

create table Log_deletions (
id int primary key auto_increment,
titulo varchar(60),
quando datetime,
quem varchar(40)
);

delimiter //

create trigger log_deletions after delete on Filmes

	for each row
	begin
		insert into Log_deletions values (null, old.titulo, sysdate(), user());
end;

//
delimiter ;

-- Relatorio tabela 

delete from filmes where id = 2;

delete from filmes where id = 4;

select * from log_deletions;

