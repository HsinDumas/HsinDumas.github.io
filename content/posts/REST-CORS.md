---
title: REST-CORS
date: 2018-09-13 16:54:55
categories:
- JAVA
tags: 
- CORS
---

> 为了满足部分接口跨域的需求，后来自己写了filter

```java
/**
 * @author dumas
 * @date 2018/8/16
 */
public class CorsFilter implements Filter {
  private static final Logger logger = LoggerFactory.getLogger(CorsFilter.class);

  private List<String> origins = Collections.singletonList("http://*.*.com.cn");
  private String headers = "Origin, X-Requested-With, Content-Type, Accept";
  private String methods = "POST";
  private List<String> urls = new Vector<>();

  @Override
  public void init(FilterConfig filterConfig) throws ServletException {
    String p = filterConfig.getInitParameter("origin");
    if (p != null) {
      origins = Arrays.asList(p.split(","));
    }
    p = filterConfig.getInitParameter("headers");
    headers = p == null ? headers : p;
    p = filterConfig.getInitParameter("methods");
    methods = p == null ? methods : p;
    p = filterConfig.getInitParameter("url");
    if (p != null) {
      urls.addAll(Arrays.asList(p.split(",")));
    }
  }

  @Override
  public void doFilter(
      ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain)
      throws IOException, ServletException {
    if (servletRequest instanceof HttpServletRequest) {
      HttpServletRequest req = (HttpServletRequest) servletRequest;
      if (!urls.isEmpty()
          && urls.stream()
              .anyMatch(url -> Pattern.compile(url).matcher(req.getServletPath()).matches())) {
        String originHeader = req.getHeader("Origin");
        if (origins.contains(originHeader)) {
          HttpServletResponse httpResponse = (HttpServletResponse) servletResponse;
          httpResponse.addHeader("Access-Control-Allow-Origin", originHeader);
          httpResponse.setHeader("Access-Control-Allow-Headers", headers);
          httpResponse.setHeader("Access-Control-Allow-Methods", methods);
        }
      }
    }
    filterChain.doFilter(servletRequest, servletResponse);
  }

  @Override
  public void destroy() {}
}

```