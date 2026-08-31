<!DOCTYPE html><html xmlns:th="http://www.thymeleaf.org"><head><meta charset="UTF-8"><title>Admin Dashboard</title><link rel="stylesheet" th:href="@{/css/style.css}"></head>
<body><nav class="navbar"><div>Admin Panel</div><a href="/logout">Logout</a> | <a href="/">View Site</a></nav>
<div class="section"><h2>Add Publication</h2>
<form th:action="@{/admin/pub/add}" th:object="${newPub}" method="post"><input th:field="*{title}" placeholder="Title" required><input th:field="*{author}" placeholder="Author"><input th:field="*{year}" type="number" placeholder="Year"><input th:field="*{keywords}" placeholder="Keywords"><input th:field="*{scholarLink}" placeholder="Scholar Link"><button class="btn">Add Publication</button></form>
<table><tr th:each="p:${publications}"><td th:text="${p.title}"></td><td><form th:action="@{'/admin/pub/delete/'+${p.id}}" method="post"><button>Delete</button></form></td></tr></table>

<h2 style="margin-top:2rem">Add News / Event</h2>
<form th:action="@{/admin/news/add}" th:object="${newNews}" method="post"><input th:field="*{title}" placeholder="Event Title" required><input th:field="*{date}" type="date"><input th:field="*{type}" placeholder="Type: Seminar/Press"><textarea th:field="*{content}" placeholder="Details"></textarea><button class="btn">Add News</button></form>
</div
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>package com.nexusri.model;
import jakarta.persistence.*;
import java.time.LocalDate;
@Entity
public class News {
  @Id @GeneratedValue Long id;
  public String title; public LocalDate date; public String type; public String content;
  public News(){}
  public News(String t, LocalDate d, String ty, String c){title=t;date=d;type=ty;content=c;}
}package com.nexusri.repository;
import com.nexusri.model.News;
import org.springframework.data.jpa.repository.JpaRepository;
public interface NewsRepository extends JpaRepository<News, Long> {}package com.nexusri.config;
import org.springframework.context.annotation.*;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.core.userdetails.*;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
public class SecurityConfig {
  @Bean
  public SecurityFilterChain filter(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(a -> a.requestMatchers("/admin/**").authenticated().anyRequest().permitAll())
        .formLogin(f -> f.loginPage("/admin/login").defaultSuccessUrl("/admin", true).permitAll())
        .logout(l -> l.logoutSuccessUrl("/").permitAll())
        .csrf(c -> c.disable());
    return http.build();
  }
  @Bean
  public UserDetailsService users(){
    var admin = User.builder().username("admin").password(new BCryptPasswordEncoder().encode("admin123")).roles("ADMIN").build();
    return new InMemoryUserDetailsManager(admin);
  }
}package com.nexusri.controller;
import com.nexusri.model.*;
import com.nexusri.repository.*;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/admin")
public class AdminController {
  @Autowired PublicationRepository pubRepo;
  @Autowired NewsRepository newsRepo;

  @GetMapping("/login") public String login(){ return "admin-login"; }

  @GetMapping("") public String dashboard(Model m){
    m.addAttribute("publications", pubRepo.findAll());
    m.addAttribute("newsList", newsRepo.findAll());
    m.addAttribute("newPub", new Publication());
    m.addAttribute("newNews", new News());
    return "admin-dashboard";
  }
  @PostMapping("/pub/add") public String addPub(@ModelAttribute Publication p){ pubRepo.save(p); return "redirect:/admin"; }
  @PostMapping("/pub/delete/{id}") public String delPub(@PathVariable Long id){ pubRepo.deleteById(id); return "redirect:/admin"; }

  @PostMapping("/news/add") public String addNews(@ModelAttribute News n){ newsRepo.save(n); return "redirect:/admin"; }
  @PostMapping("/news/delete/{id}") public String delNews(@PathVariable Long id){ newsRepo.deleteById(id); return "redirect:/admin"; }
}<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><title>Admin Login</title><link rel="stylesheet" th:href="@{/css/style.css}"></head>
<body class="section"><h2>Staff Login</h2><p>Secure access for institute staff - WCAG compliant</p>
<form method="post" action="/admin/login"><input name="username" placeholder="Username" required><input name="password" type="password" placeholder="Password" required><button class="btn">Login</button></form>
<p>Default: admin / admin123</p></body></html><!DOCTYPE html><html xmlns:th="http://www.thymeleaf.org"><head><meta charset="UTF-8"><title>Admin Dashboard</title><link rel="stylesheet" th:href="@{/css/style.css}"></head>
<body><nav class="navbar"><div>Admin Panel</div><a href="/logout">Logout</a> | <a href="/">View Site</a></nav>
<div class="section"><h2>Add Publication</h2>
<form th:action="@{/admin/pub/add}" th:object="${newPub}" method="post"><input th:field="*{title}" placeholder="Title" required><input th:field="*{author}" placeholder="Author"><input th:field="*{year}" type="number" placeholder="Year"><input th:field="*{keywords}" placeholder="Keywords"><input th:field="*{scholarLink}" placeholder="Scholar Link"><button class="btn">Add Publication</button></form>
<table><tr th:each="p:${publications}"><td th:text="${p.title}"></td><td><form th:action="@{'/admin/pub/delete/'+${p.id}}" method="post"><button>Delete</button></form></td></tr></table>

<h2 style="margin-top:2rem">Add News / Event</h2>
<form th:action="@{/admin/news/add}" th:object="${newNews}" method="post"><input th:field="*{title}" placeholder="Event Title" required><input th:field="*{date}" type="date"><input th:field="*{type}" placeholder="Type: Seminar/Press"><textarea th:field="*{content}" placeholder="Details"></textarea><button class="btn">Add News</button></form>
</div></body></html>
