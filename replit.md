# Overview

This is a modern, responsive corporate website for ANKAPANJUR, an automation shutter and door systems company. The application is built as a full-stack web solution showcasing the company's products, services, and expertise in industrial automation systems. The website features a clean, professional design with Turkish language content, targeting customers interested in automated shutter, door, and security systems.

**Complete Content Management System**: The website features a comprehensive admin panel where ALL text content can be edited dynamically through the database, including homepage features, about page values and achievements, company information, and site settings. No more static text - everything is editable through the admin interface.

**Technology Stack**: Built with Node.js, Express, React, TypeScript, and PostgreSQL for modern, scalable web development.

**Deployment Ready**: Project has been cleaned and comprehensive deployment documentation created. Complete step-by-step installation guide (SIFIRDAN_KURULUM.md) available for Ubuntu 22.04 VDS deployment (IP: 89.47.113.251, domain: ankapanjur.com).

# User Preferences

Preferred communication style: Simple, everyday language.
Technology preference: Node.js/React stack only (PHP alternative removed).
Deployment target: Ubuntu 22.04 VDS (Windows development files removed).
Installation preference: Manual step-by-step process rather than automated scripts.

# System Architecture

## Frontend Architecture
- **React with TypeScript**: Built using Vite as the build tool for fast development and optimized production builds
- **UI Framework**: Utilizes shadcn/ui components built on top of Radix UI primitives for accessible, customizable components
- **Styling**: Tailwind CSS with custom CSS variables for theming, supporting both light and dark modes
- **Routing**: Client-side routing implemented with Wouter for lightweight navigation
- **State Management**: React Query (TanStack Query) for server state management and API data fetching
- **Forms**: React Hook Form with Zod validation for type-safe form handling

## Backend Architecture
- **Express.js Server**: RESTful API server with TypeScript support
- **Development Setup**: Hot module replacement and development middleware via Vite integration
- **API Structure**: Modular route handling with centralized error management
- **Request Logging**: Custom middleware for API request/response logging

## Database Schema
- **ORM**: Drizzle ORM configured for PostgreSQL with type-safe database operations
- **Schema Design**: 
  - Contact submissions table for capturing customer inquiries
  - User management system with admin authentication
  - Services and projects tables for content management
  - Homepage features, about values, and about achievements for dynamic text content
  - Company info and site settings for global configuration
  - UUID-based primary keys with automatic generation
- **Content Management**: All text content is stored in database and editable through admin panel

## Component Architecture
- **Modular Design**: Reusable components for services, projects, and contact forms
- **Responsive Layout**: Mobile-first design with consistent header, footer, and navigation
- **Interactive Elements**: Modal dialogs for image galleries, form submissions with toast notifications
- **Loading States**: Custom loading screen with company branding

## Content Management
- **Static Content**: Company information, services, and projects stored in TypeScript constants
- **Multilingual Support**: Turkish language content with proper SEO meta tags
- **Asset Management**: Optimized image handling with proper alt text and responsive images

# External Dependencies

## Core Framework Dependencies
- **React 18**: Frontend framework with TypeScript support
- **Express.js**: Backend web framework
- **Vite**: Build tool and development server
- **Drizzle ORM**: Type-safe ORM for PostgreSQL integration

## UI and Styling
- **shadcn/ui**: Complete UI component library built on Radix UI
- **Tailwind CSS**: Utility-first CSS framework
- **Radix UI**: Headless UI primitives for accessibility
- **Lucide React**: Icon library for consistent iconography

## Database Integration
- **PostgreSQL**: Primary database (configured for Neon serverless)
- **@neondatabase/serverless**: Serverless PostgreSQL driver
- **Drizzle Kit**: Database migration and schema management tool

## Development Tools
- **TypeScript**: Type safety across the entire application
- **ESBuild**: Fast JavaScript bundler for production builds
- **PostCSS**: CSS processing with Autoprefixer

## Form Handling and Validation
- **React Hook Form**: Form state management and validation
- **Zod**: Schema validation library for type-safe data parsing
- **@hookform/resolvers**: Integration between React Hook Form and Zod

## Additional Features
- **React Query**: Server state management and caching
- **Wouter**: Lightweight client-side routing
- **date-fns**: Date manipulation and formatting utilities
- **Class Variance Authority**: Utility for creating variant-based component APIs